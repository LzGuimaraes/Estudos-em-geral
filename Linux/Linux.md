# 📘 Guia de Comandos Linux

Este documento traz os **principais comandos Linux**, suas explicações e exemplos práticos.  
Ideal para iniciantes e intermediários que querem se familiarizar com o terminal.  

---

## 🖥️ Navegação entre diretórios

### `pwd` (print working directory)
Mostra o diretório atual.
```bash
pwd
```

### `ls` (list)
Lista arquivos e diretórios.
```bash
ls          # lista simples
ls -l       # lista detalhada
ls -a       # inclui arquivos ocultos
ls -lh      # lista detalhada com tamanho legível
```

### `cd` (change directory)
Muda de diretório.
```bash
cd /home/user       # vai para /home/user
cd ..               # volta um diretório
cd ~                # vai para a pasta do usuário
```

---

## 📂 Manipulação de arquivos e diretórios

### `touch`
Cria um arquivo vazio.
```bash
touch arquivo.txt
```

### `mkdir`
Cria um diretório.
```bash
mkdir projetos
mkdir -p dev/frontend   # cria estrutura de diretórios
```

### `cp`
Copia arquivos ou diretórios.
```bash
cp arquivo.txt copia.txt
cp -r pasta pasta_copia   # copia diretório inteiro
```

### `mv`
Move ou renomeia arquivos.
```bash
mv arquivo.txt documentos/
mv arquivo.txt novo_nome.txt
```

### `rm`
Remove arquivos ou diretórios.
```bash
rm arquivo.txt
rm -r pasta   # apaga pasta e conteúdo (cuidado!)
```

---

## 📖 Visualizar arquivos

### `cat`
Exibe conteúdo de arquivos.
```bash
cat arquivo.txt
```

### `less`
Mostra arquivo página por página.
```bash
less arquivo.txt
```

### `head` e `tail`
Exibem as primeiras ou últimas linhas.
```bash
head -n 10 arquivo.txt   # primeiras 10 linhas
tail -n 20 arquivo.txt   # últimas 20 linhas
tail -f log.txt          # acompanha log em tempo real
```

---

## 🔍 Busca e filtros

### `find`
Busca arquivos.
```bash
find /home -name "arquivo.txt"
find . -type d -name "projetos"
```

### `grep`
Procura texto dentro de arquivos.
```bash
grep "palavra" arquivo.txt
grep -r "erro" /var/log/
```

### `wc` (word count)
Conta linhas, palavras e caracteres.
```bash
wc arquivo.txt
wc -l arquivo.txt   # número de linhas
```

---

## ⚙️ Permissões e usuários

### `chmod`
Altera permissões.
```bash
chmod 755 script.sh
chmod +x script.sh   # adiciona permissão de execução
```

### `chown`
Muda dono de um arquivo.
```bash
chown usuario:grupo arquivo.txt
```

### `whoami`
Mostra usuário atual.
```bash
whoami
```

---

## 📦 Gerenciamento de pacotes (Debian/Ubuntu)

### `apt-get update` e `apt-get upgrade`
Atualizam lista e pacotes.
```bash
sudo apt-get update
sudo apt-get upgrade
```

### `apt install`
Instala programas.
```bash
sudo apt install git
```

### `apt remove`
Remove programas.
```bash
sudo apt remove nome_pacote
```

---

## 🖧 Rede

### `ping`
Testa conectividade.
```bash
ping google.com
```

### `curl` e `wget`
Fazem requisições HTTP.
```bash
curl https://example.com
wget https://example.com/arquivo.zip
```

### `ifconfig` ou `ip`
Mostra informações de rede.
```bash
ip addr
```

---

## 📝 Processos e sistema

### `top` / `htop`
Mostram processos em execução.
```bash
top
```

### `ps`
Lista processos.
```bash
ps aux
ps -ef | grep firefox
```

### `kill`
Mata processos.
```bash
kill 1234         # mata processo pelo PID
kill -9 1234      # força matar
```

---

## 📦 Compactação e arquivos

### `tar`
Compacta ou descompacta.
```bash
tar -cvf arquivo.tar pasta/
tar -xvf arquivo.tar
```

### `zip` e `unzip`
Compacta e descompacta em `.zip.
```bash
zip arquivo.zip arquivo.txt
unzip arquivo.zip
```

---

## 🔑 Superusuário

### `sudo`
Executa comando como administrador.
```bash
sudo apt install htop
```

### `su`
Troca para outro usuário.
```bash
su root
```

---

## 🐧 Dicas finais
- Use `man comando` para abrir o manual de um comando.
- Combine comandos com `|` (pipe).
```bash
ps aux | grep firefox
```
- Use `&&` para rodar comandos em sequência somente se o anterior for bem sucedido.
```bash
mkdir pasta && cd pasta
```

---

## ✅ Resumão

- **Navegação:** `pwd`, `ls`, `cd`
- **Arquivos:** `touch`, `mkdir`, `cp`, `mv`, `rm`
- **Leitura:** `cat`, `less`, `head`, `tail`
- **Busca:** `find`, `grep`, `wc`
- **Permissões:** `chmod`, `chown`, `whoami`
- **Pacotes (Debian/Ubuntu):** `apt update`, `apt install`
- **Rede:** `ping`, `curl`, `wget`, `ip`
- **Processos:** `top`, `ps`, `kill`
- **Compactação:** `tar`, `zip`, `unzip`
- **Administração:** `sudo`, `su`

---

# Desafios
**1. Crie uma pasta de estudos no seu diretório pessoal**
```bash
mkdir ~/desafio_linux
cd ~/desafio_linux
```
**2. Dentro dela, crie três subpastas**
```bash
mkdir ~/desafio_linux
mkdir notas projetos logs
```
**3. Entre na pasta `notas` e crie três arquivos de texto:**
```bash
cd notas
touch linux.txt c++.txt python.txt
```
**4. Escreva algo dentro de cada arquivo usando `echo` + redirecionamento**
```bash
echo "Aprendendo comandos Linux" > linux.txt
echo "Estudando POO e ponteiros" > c++.txt
echo "Automação e scripts em Python" > python.txt
```
**5. Verifique o conteúdo de cada arquivo:**
```bash
cat linux.txt
cat c++.txt
cat python.txt
```
**6. Copie o arquivo `linux.txt` para a pasta `logs`:**
```bash
cp linux.txt ../logs/
```
**7. Renomeie c++.txt para cpp.txt:**
```bash
mv c++.txt cpp.txt
```
**8. Volte para a pasta raiz do desafio e crie um arquivo de log vazio chamado historico.log**
```bash
cd ..
touch historico.log
```
**9. Liste todos os arquivos e pastas criados até agora (com detalhes):**
```bash
ls -lhR
```
**10. Procure a palavra "Python" dentro de todos os arquivos da pasta `notas`:**
```bash
grep "Python" notas/*
```
**11. Compacte a pasta notas em um arquivo notas.tar:**
```bash
grep "Python" notas/*
```
**12. Remova a pasta `notas` inteira (simulação de backup):**
```bash
rm -r notas
```
**13. Extraia novamente o arquivo notas.tar para recuperar a pasta:**
```bash
tar -xvf notas.tar
```

# 🐧 Desafios Linux no Terminal
## 🔹 Nível 1 – Básico
#### Objetivo: praticar manipulação, cópia, busca e permissões.
1. Crie uma pasta

```bash
mkdir ~/basico && cd ~/basico

```
2. Crie 2 arquivos de texto (`info.txt` e `tarefas.txt`).

3.  Escreva no info.txt
```bash
Meu nome é <seu_nome>
Estou aprendendo Linux
```
4. No arquivo `tarefas.txt`, adicione 3 tarefas fictícias.
   
5. Liste os arquivos criados com `ls -l`.

6. Mostre o conteúdo dos arquivos com `cat`.
--- 
## 🔹 Nível 2 – Intermediário
#### Objetivo: praticar manipulação, cópia, busca e permissões.

1. Crie uma pasta `intermediario` com subpastas `projetos` e `logs`.

2. Crie dentro de projetos os arquivos: `projeto1.txt`, `projeto2.txt`, `projeto3.txt`.(Cada um deve ter uma frase dentro )

3. Copie o arquivo `projeto1.txt` para a pasta logs.

4. Renomeie `projeto2.txt` para `projeto_final.txt`.

5. Use `grep` para procurar uma palavra que você escreveu nos arquivos.

6. Conte quantas linhas existem em `projeto3.txt` usando `wc -l`.

7. Dê permissão de execução `(chmod +x)` em `projeto3.txt`.

---

## 🔹 Nível 3 – Avançado
#### Objetivo: trabalhar com compressão, processos e pipelines.
1. Crie uma pasta `avancado` e dentro dela uma subpasta dados.
2. Baixe um arquivo de texto da internet (se tiver `wget` ou `curl` instalado). Exemplo:
```bash
wget https://www.gutenberg.org/files/1342/1342-0.txt -O dados/livro.txt
```
3. Mostre apenas as 10 primeiras linhas do arquivo com `head`.
4. Conte quantas vezes a palavra love aparece no arquivo (`grep -o love | wc -l`).
5. Compacte a pasta `dados` em um arquivo `dados.tar.gz.`
6. Apague a pasta `dados`.
7. Extraia o arquivo `dados.tar.gz` para recuperar a pasta.
8. Rode um processo em segundo plano (por exemplo: `sleep 100 &`).
   - Liste os processos com `ps`.
   - Encontre o PID do processo `sleep`.
   - Mate o processo com `kill`.

Dica de ouro sempre que tiver dúvida rode o comando:
```bash
man comando
```
