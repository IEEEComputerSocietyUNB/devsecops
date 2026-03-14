--- 
sidebar_position: 2 
--- 

# Scripting

É inegável a importância dos scripts para o Linux. Se você for parar pra pensar, até mesmo comandos como _vim_ e _cat_ funcionam como scripts. Nesta lição de hoje, vamos dar uma olhada com scripts no Shell (mais especificamente o Bash, shell padrão de diversas distros do Linux) e um pouco de Python também!

## Bash

Antes de aprendermos scrips em bash, bora aprender uns comandos básicos?

```console
cd 
ls 
ls -a
ls -l 
chmod +x  
pwd
mkdir
cat 
grep
grep -r 
grep -i 
wc 
|
&&
>
>>
```

## Scripts com Bash

### Exemplo do Chuck

```bash
#!/bin/bash 
#acima temos o shebang, que serve pra dizer qual interpretador (e o caminho pra ele) será usado pra executar o Bash!

echo "What is your name?"

read name

echo "How old are you?"

read age

echo "Hello $name, you are $age years old."

sleep 2

echo "Calculating"
echo "............"
sleep 1
echo "**.........."
sleep 1
echo "****........"
sleep 1
echo "******......"
sleep 1
echo "********...."
sleep 1
echo "**********.."
sleep 1
echo "************"


getrich=$((( $RANDOM % 15 ) + $age )) #O cifrão 

echo "$name, you will become a millionaire when you are $getrich years old"
```

### Exemplo da Nana

[Handbook sobre scripts](https://mcusercontent.com/fb53aa76bbc82533008812f2f/files/f933db55-6fef-597a-c669-48cab314418e/TWN_Bash_Scripting_Handout.01.pdf)

```bash
#!/bin/bash
LOG_DIR="/Users/nana/logs"
ERROR_PATTERNS=("ERROR" "FATAL" "CRITICAL")

echo -e "\n==========================================================="
echo "==== Analysing log files in $LOG_DIR directory ===="
echo "==========================================================="
echo -e "\n### List of log files updated in last 24 hours ###"

LOG_FILES=$(find "$LOG_DIR" -name "*.log" -mtime -1) 
#o * no regex informa que há uma quantidade indefinida de caracteres antes, enquanto o -mtime 
#indica o tempo de modificação, com -1 representando há 24h, ou seja, 
#arquivos que foram modificados desde ontem.

echo "$LOG_FILES"

# Loop through each log file
for LOG_FILE in $LOG_FILES; do
 echo -e "\n==================================================="
 echo "============ $LOG_FILE ============"
 echo "==================================================="

 echo -e "\n### searching ${ERROR_PATTERNS[0]} logs in $LOG_FILE ###"
 grep "${ERROR_PATTERNS[0]}" "$LOG_FILE"

 echo -e "\n### Number of ${ERROR_PATTERNS[0]} logs found ###"
 grep -c "${ERROR_PATTERNS[0]}" "$LOG_FILE"

 # Additional commands omitted for brevity
done
```

## Python

Para exemplos usando Python:

[Exemplo 1](https://www.baeldung.com/linux/bash-script-run-python)

[Exemplo 2](https://www.codementor.io/@chirilovadrian360/using-bash-and-python-together-with-samples-28jpip8vmu)

## Recursos adicionais e inspirações

Alguns dos vídeos que assisti antes pra me preparar - e sim, não subestimem o poder dos vídeos!

[TechWorld with Nana](https://www.youtube.com/watch?v=PNhq_4d-5ek)

[freeCodeCamp](https://www.youtube.com/watch?v=tK9Oc6AEnR4)

[Network Chuck](https://www.youtube.com/watch?v=19nN9vgcgmU)
