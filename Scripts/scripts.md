#!/bin/bash
# ============================================================
# Scripts usados nos experimentos da AV1
# Disciplina: Sistemas Operacionais — UFS 2026.2
# Discente: Augusto César Honorato dos Santos
# ============================================================

# ------------------------------------------------------------
# INVENTÁRIO DO AMBIENTE
# Coleta informações básicas sobre o sistema onde os testes
# foram executados.
# ------------------------------------------------------------

# Mostra informações do kernel e da arquitetura do sistema
uname -a

# Exibe a distribuição Linux e sua versão (ex: Ubuntu 24.04.2)
cat /etc/os-release

# Lista detalhes da CPU: modelo, núcleos, threads, cache, etc.
lscpu

# Mostra o uso de memória RAM e swap em formato legível (-h)
free -h

# Exibe o número de núcleos de CPU disponíveis
nproc

# Mostra o uso do disco em formato legível (-h)
df -h

# Exibe a versão do Ollama instalada
ollama --version

# Lista todos os modelos baixados no Ollama
ollama list

# ------------------------------------------------------------
# PROCESSOS E THREADS
# Identifica os processos e threads do Ollama em execução.
# ------------------------------------------------------------

# Lista todos os processos do sistema e filtra os que contêm "ollama"
# Mostra PID, PPID, usuário, comando, etc.
ps -ef | grep ollama

# Lista processos E suas threads (-L), em formato completo (-f)
# Revela quantas threads cada processo do Ollama possui
ps -eLf | grep ollama

# ------------------------------------------------------------
# CHAMADAS DE SISTEMA (STRACE)
# Analisa quais syscalls o processo do Ollama executa.
# ------------------------------------------------------------

# Anexa o strace ao processo do Ollama (encontrado via pgrep)
# -f: segue processos filhos (threads)
# -c: gera um resumo (contagem de chamadas por tipo)
# -p: anexa ao PID especificado
# -o: salva a saída no arquivo /tmp/strace-resumo.txt
# O pgrep -f "ollama serve" encontra o PID do processo principal
sudo strace -f -c -p $(pgrep -f "ollama serve") -o /tmp/strace-resumo.txt

# ------------------------------------------------------------
# EXPERIMENTOS DE INFERÊNCIA
# Mede o tempo de execução de requisições ao modelo.
# ------------------------------------------------------------

# time: mede o tempo real, de usuário e de sistema do comando
# ollama run: envia um prompt ao modelo especificado
# Teste 1: prompt longo (explicação técnica) — deve demorar mais
time ollama run llama3.2:1b "Explique o que é um processo em Sistemas Operacionais."

# Teste 2: prompt curto (saudação) — deve demorar menos
time ollama run llama3.2:1b "Oi"
