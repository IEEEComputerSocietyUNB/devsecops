# Git & Github: O básico e a prática

Um breve guia sobre Controle de Versão utilizando as ferramentas do ambiente Git. Esse não é um texto definitivo, nem totalmente preciso, e está aberto a alterações e incrementações sugeridas por todos. São utilizados comandos comuns ao ambiente UNIX. 

## Setup

Antes de entrar propriamente no assunto, vamos configurar nosso ambiente de trabalho, partindo da premissa que a CLI do Git já está instalada em sua máquina, seja no [Windows](https://git-scm.com/install/windows), [Linux](https://git-scm.com/install/linux)  ou [MacOS](https://git-scm.com/install/mac).

Agora, como veremos mais a frente, a integridade é um dos pilares do Git, e uma das aplicações de tal principío é o conhecimento
de quem está realizando uma certa mudança no repositório. Por padrão, deve-se informar um nome e um email para submeter uma mudança. Observe que isso é um procedimento puramente local (Git), o qual não precisa, mas se recomenda fortemente, refletir suas credencias remotas (GitHub). Dessa forma, faça:

```sh
git config --global user.name 'Nome de Usuário' 
git config --global user.email 'Seu email'
```

Outra configuração importante é qual será o editor de texto padrão. No mundo ideal, clonamos ou criamos um repositório, fazemos nosso trabalho localmente e, então, fazemos upload da versão modificada para o repositório. Contudo, incompatiblidades e problemas aparecem, com solução dependente do contexto. Nesse contexto, o Git exige intervenção direta do usuário não apenas via comandos, mas também via edição de texto (mensagens de commit, rebase interativo, resolução de conflitos, etc.). Para isso, configuramos um editor padrão. Um ponto crucial é que o Git precisa aguardar o fechamento do editor para continuar sua execução — motivo pelo qual utilizamos a flag --wait ao configurar o VSCode:

```sh
git config --global core.editor "code --wait"
```

***OBS***: Isso pré-configura uma apresentação padrão para todos os seus repositórios. Caso queira mudar as credenciais para - por exemplo - usar outra conta, descarte a flag `--global` e reconfigure ao seu modo, **necessariamente dentro do repositório alvo**.

### SSH

Já é bom, nesse estágio, configurarmos nossa chave SSH, a maneira pela qual o GitHub autentica o autor da mudança, garantindo assim a integridade. Ela garantirá mais segurança (como sempre, a depender do manuseio pelo usuário) e agilidade no fluxo de trabalho do que as outras ferramentas disponiblizadas, a saber, HTTPS. Você não precisa virar um expert em criptografia para utilizar desse método, apenas mentalizar que haverá uma par de chaves **Pública-Privada**, esta derivada daquela, e que você não deve, em nenhuma hipótese, revelar a chave Privada - você pode perder desde a segurança do seu repositório até alguns Bitcoins. No terminal:

```sh
# -t denota o tipo de curva criptográfica, o padrão-ouro para o Github seno a ed25519, -c é apenas um comentário
ssh-keygen -t ed25519 -C "seu email"
```
Confirme a solicitação do terminal para escrever as chaves nos arquivos, que por padrão serão `~/.ssh/id_ed25519` (**Chave Privada**) e `~/.ssh/id_ed25519.pub` (**Chave Pública**). Então digite uma senha (passphrase) para dar ainda mais segurança, já que ela força a entrada da senha em todo uso da chave.

Um dos objetivos de adotarmos a abordagem por chave SSH é a agilidade em contraponto ao método HTTPS. Do modo como estamos agora, teríamos que entrar manualmente com nossa passphrase toda vez que usassemos a chave. Assim, a entidade `ssh-agent` será necessária para gerenciar nossa chave:

```sh
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

Se tudo aconteceu como o esperado, basta copiar o conteúdo de `~/.ssh/id_ed25519.pub` e seguir os seguintes passos do [tutorial do GitHub](https://docs.github.com/pt/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account?tool=webui).

Por fim, para testar se tudo ocorreu bem, faça:

```sh
ssh -T git@github.com
```

Retorno esperado: `Hi username! You've successfully authenticated, but GitHub does not provide shell access.`

[//]: # (Aqui vai começar a explicação teórica)

## Iniciando o meu repositório

Há diversas maneiras de iniciar um repositório. Podemos iniciá-lo localmente e integrá-lo depois. De modo a simplificar o trabalho posterior, vamos já de inicio implementar nosso repositório como remoto, e cloná-lo para nossa máquina. No GitHub, crie um novo repositório, configurando-o incialmente apenas com a licença e um arquivo `.gitignore` e o clone a partir do link SSH:

```sh
git clone git@github.com:repoOwner/repoName.git
```

Vamos adicionar um **README** ao repositório remoto:

```sh
git pull # Antes de iniciar os trabalhos em um repositório compartilhado, absorva as novidades
touch README.md # cria arquivo README.md
echo "# repoName" > README.md # Escreve no arquivo
git add README.md # Adiciona as mudanças do arquivo a Staging Area
git commit -m"docs: primeiro commit" # commitamos (nos comprometemos) com um checkpoint
git push # submetemos as mudanças ao repositório remoto (no github)
```
Essa é a maneira mais simples. Pode-se também inicializar localmente e, aí, exportar para o repositório no GitHub. Suponha que você construiu um projeto para uma matéria há algum tempo, de forma totalmete local e despretensionsa, e agora almeja aprimorá-la para figurar no seu portifólio. Temos que iniciar um diretório `.git` e também configurar o repositório remoto como upstream:

```sh
git init
git remote add origin git@github.com:repoOwner/repoName.git
git push -u origin <branch>
```
Nesse caso, de um repositório pessoal e inicial ,`<branch> = main`. Mas precisamos saber exatamente o que significa uma branch.

## Branch: uma linha paralela no desenvolvimento

Uma branch (ramificação) nada mais é que uma linha de desenvolvimento criada a partir de um certo estado do repositório, que tem sua própria história independente da linha principal e de outros branches. Você pode implementar suas próprias modificações, testes e erros sem afetar a linha principal, e solicitar o `merge` quando se sentir confortável para compartilhar seu trabalho. Para criar uma branch:

```sh
git switch -c nome-da-branch
```
Como mencionado acima, deve-se configurar o branch remoto (criado no GitHub a partir da área **Branches**) de upstream do branch local:

```sh
git push -u origin nome-da-branch-remota
```

### Pull Request

Um Pull Request (PR) é uma solicitação para integrar as mudanças de uma branch em outra (geralmente na `main`, mas não necessariamente). Ele pode ser criado na aba **Pull Requests** do repositório no GitHub.

Durante esse processo, é comum que surjam incompatibilidades entre as duas branches — especialmente quando ambas modificaram as mesmas partes do código. Esses casos são chamados de **conflitos de merge** e são detectados automaticamente pelo Git. A resolução desses conflitos exige intervenção manual: o desenvolvedor precisa revisar as diferenças, decidir qual versão manter (ou como combiná-las) e então atualizar a branch. Esse processo pode envolver comandos mais avançados do Git e o uso do editor configurado previamente.

## Release x Deploy

Ao entrar em um repositório no GitHub, você deve ter reparado em duas opções: Deployments e Releases. Deploy é o processo técnico de instalar e mover o código da aplicação para um servidor ou ambiente, sem necessariamente liberá-lo ao usuário. Release é a disponibilização funcional desse código para os usuários finais, que pode ocorrer logo após o deploy ou separadamente.


### 📦 Release no GitHub

Uma **Release** no contexto GitHub é versão oficial e identificada do seu projeto, associada a uma tag é. Ela serve para:

- marcar versões (ex: `v1.0.0`)
- anexar binários/artefatos
- documentar mudanças (changelog)

Crie um release via browser:

1. Vá no repositório
2. Aba **Releases**
3. Clique em **Draft a new release**
4. Crie uma tag (ex: `v1.0.0`)
5. Adicione descrição
6. Publique



[//]: # (Aqui vem a explicação mais "complicada", Rebase, Reset, etc...)

## Boas Práticas de Commit

Há algumas convenções de formato de commit - ou nomeação de uma Branch - com o intuito de deixá-lo transparente e direto-ao-ponto. Assim dado `git commit -m"TAG: msg"`, a `TAG`pode ser:

-`docs`: Sinaliza mudanças na documentação, por exemplo, no `README`

-`test`: Adição ou modificação de testes

-`feat`: Adição de nova funcionalidade

-`chore`: Tarefas gerais que não afetam diretamente o código da aplicação. Ex: Atualização de dependências, remoção de arquivos temporários e ajuste de variáveis de ambiente. Há intersecção entre outros tipos mais especificos como `ci`e `build`.

-`refactor`: [Refatorar](https://refactoring.guru/refactoring) significa tornar um código mais legível/mais organizado, sem criar novas funcionalidades ou alterar o comportamento de uma. Inclui renomeação de variáveis, adição de comentários, modularização...

-`ci`: Mudanças relacionadas ao CI/CD, como adição de pipeline de testes no Actions.

-`build`: Mudanças no processo de Build/Compilação. Ex: Makefile.

-`perf`: Mudanças em alguma função/algoritmo com o fito de aumentar a perfomance. Ex: Redução de complexidade O(n²) para O(n) (Perdão aos amigos da Elétrica).

## Bibliografia

- https://git-scm.com/learn
- https://akitaonrails.com/2020/02/05/akitando-70-entendendo-git-nao-e-um-tutorial/
- https://www.atlassian.com/br/git/tutorials/