# Scripts

## inventario.sh

```bash
#!/bin/bash
uname -a
cat /etc/os-release
lscpu
free -h
nproc
df -h
ollama --version
ollama list
```

## processos_threads.sh

```bash
#!/bin/bash
ps -ef | grep ollama
ps -eLf | grep ollama
```

## strace.sh

```bash
#!/bin/bash
sudo strace -f -c -p $(pgrep -f "ollama serve") -o /tmp/strace-resumo.txt
```

## experimentos.sh

```bash
#!/bin/bash
time ollama run llama3.2:1b "Explique o que é um processo em Sistemas Operacionais."
time ollama run llama3.2:1b "O que é uma thread?"
time ollama run llama3.2:1b "Explique escalonamento de CPU."
time ollama run llama3.2:1b "Escreva um resumo de 300 palavras sobre memória virtual."
time ollama run llama3.2:1b "Oi"
time ollama run llama3.2:1b "Tudo bem?"
time ollama run llama3.2:1b "Qual a capital do Brasil?"
time ollama run llama3.2:1b "Diga apenas: ok"
```

## concorrencia.sh

```bash
#!/bin/bash
time (ollama run llama3.2:1b "Explique threads" & ollama run llama3.2:1b "Explique processos" & wait)
time (ollama run llama3.2:1b "Explique memória" & ollama run llama3.2:1b "Explique CPU" & wait)
time (ollama run llama3.2:1b "O que é deadlock?" & ollama run llama3.2:1b "O que é semáforo?" & wait)
time (ollama run llama3.2:1b "O que é paginação?" & ollama run llama3.2:1b "O que é swapping?" & wait)
```

## logs.sh

```bash
#!/bin/bash
cat /tmp/experimentos_longos.txt /tmp/experimentos_curtos.txt /tmp/experimentos_concorrencia.txt /tmp/strace-resumo.txt > /tmp/todos_logs.txt
cat /tmp/todos_logs.txt
```
