**Navegação**

```bash id="70341"
pwd               # mostra o diretório atual
ls                # lista arquivos
ls -l             # lista detalhada
ls -la            # inclui arquivos ocultos
cd pasta          # entra na pasta
cd ..             # volta uma pasta
cd ~              # vai para a home
tree              # mostra estrutura em árvore
```

**Arquivos e diretórios**

```bash id="70342"
file arquivo      # identifica o tipo do arquivo
stat arquivo      # mostra detalhes do arquivo
touch arquivo.txt # cria arquivo vazio
mkdir pasta       # cria pasta
mkdir -p a/b/c    # cria pastas aninhadas
cp a.txt b.txt    # copia arquivo
cp -r pasta1 pasta2   # copia pasta
mv a.txt pasta/   # move ou renomeia
rm arquivo.txt    # remove arquivo
rm -r pasta       # remove pasta
rm -rf pasta      # remove pasta à força
```

**Visualização de conteúdo**

```bash id="70343"
cat arquivo.txt       # exibe o conteúdo inteiro
less arquivo.txt      # navega pelo conteúdo
more arquivo.txt      # visualização simples
head arquivo.txt      # primeiras linhas
head -n 20 arquivo.txt
tail arquivo.txt      # últimas linhas
tail -n 20 arquivo.txt
tail -f log.txt       # acompanha log em tempo real
nl arquivo.txt        # mostra linhas numeradas
```

**Busca e filtro**

```bash id="70344"
find . -name "flag.txt"         # procura arquivo pelo nome
find / -type f -name "*.txt" 2>/dev/null
locate arquivo                  # busca rápida por índice
which python3                   # mostra caminho do comando
whereis binwalk                 # localiza binário/manual
grep "texto" arquivo.txt        # busca texto
grep -r "senha" .               # busca recursiva
grep -i "admin" arquivo.txt     # ignora maiúsc/minúsc
strings arquivo.bin             # extrai strings legíveis
strings arquivo.bin | grep flag
```

**Compactação e extração**

```bash id="70345"
unzip arquivo.zip
zip -r saida.zip pasta/
tar -xf arquivo.tar
tar -xzf arquivo.tar.gz
tar -xjf arquivo.tar.bz2
gzip arquivo
gunzip arquivo.gz
7z x arquivo.7z
```

**Permissões**

```bash id="70346"
chmod +x script.sh          # torna executável
chmod 644 arquivo.txt
chmod 755 script.sh
chown usuario:usuario arq   # muda dono
sudo comando                # executa como root
```

**Hashes e integridade**

```bash id="70347"
md5sum arquivo
sha1sum arquivo
sha256sum arquivo
sha512sum arquivo
echo -n "texto" | sha256sum
```

**Informações do sistema**

```bash id="70348"
whoami              # usuário atual
id                  # uid, gid, grupos
hostname            # nome da máquina
uname -a            # info do sistema/kernel
lsb_release -a      # versão da distro
df -h               # uso de disco
du -sh pasta/       # tamanho da pasta
free -h             # memória RAM
ps aux              # processos
top                 # monitor de processos
htop                # versão melhorada, se instalada
```

**Rede**

```bash id="70349"
ip a                    # interfaces e IPs
ip route                # rotas
ping 8.8.8.8
ss -tulpn               # portas e serviços
netstat -tulpn          # equivalente antigo
curl http://site.com
wget http://site.com/arquivo
nc -lvnp 4444           # netcat escutando
nc 192.168.1.10 4444    # netcat conectando
```

**Pacotes e instalação**

```bash id="70350"
sudo apt update
sudo apt upgrade
sudo apt install nome-do-pacote
sudo apt remove nome-do-pacote
```

**Análise útil para CTF/forense**

```bash id="70351"
xxd arquivo.bin                 # hex dump
hexdump -C arquivo.bin
binwalk arquivo.bin             # identifica conteúdo embutido
binwalk -e arquivo.bin          # extrai
exiftool imagem.jpg             # metadados
file arquivo
strings arquivo | less
base64 arquivo.txt
base64 -d arquivo.b64
```

**Processamento de texto**

```bash id="70352"
sort lista.txt
uniq lista.txt
sort lista.txt | uniq
cut -d ':' -f1 arquivo.txt
awk '{print $1}' arquivo.txt
sed -n '1,20p' arquivo.txt
wc -l arquivo.txt       # conta linhas
wc -c arquivo.txt       # conta bytes
```

**Ajuda**

```bash id="70353"
man comando
comando --help
history
clear
```

**Fluxo bem comum em CTF**

```bash id="70354"
file arquivo
strings arquivo | less
binwalk arquivo
xxd arquivo | head
exiftool arquivo
sha256sum arquivo
```
