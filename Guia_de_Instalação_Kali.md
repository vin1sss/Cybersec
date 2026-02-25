# Guia Completo de Instalação

Este guia descreve, passo a passo, como:

* Instalar o VirtualBox
* Baixar uma máquina virtual (VM) do Kali Linux
* Importar e executar o Kali Linux no VirtualBox

---

## 1. Instalação do VirtualBox

### 1.1 Download do VirtualBox

1. Acesse o site oficial do VirtualBox: https://www.virtualbox.org/
<img width="1839" height="862" alt="image" src="https://github.com/user-attachments/assets/b24e76c1-cc6d-4761-9582-dee45e0302da" />

<br/>

2. Vá até a seção **Downloads**
<img width="1843" height="867" alt="image" src="https://github.com/user-attachments/assets/d2beeab3-10bc-4f6e-abf8-927e70313ef3" />


3. Baixe a versão correspondente ao seu sistema operacional:

   * Windows hosts
   * macOS hosts
   * Linux distributions

> Sempre utilize a versão mais recente estável.

### 2.2 Instalação no Windows

1. Execute o instalador `.exe`
2. Avance mantendo as opções padrão
3. Confirme a instalação de drivers de rede quando solicitado
4. Conclua a instalação

### 2.3 Instalação no Linux (Ubuntu/Debian)

```bash
sudo apt update
sudo apt install virtualbox
```

### 2.4 Instalação no macOS

1. Abra o arquivo `.dmg`
2. Arraste o VirtualBox para a pasta **Applications**
3. Caso o macOS bloqueie a instalação, libere em:

   * Configurações do Sistema > Segurança e Privacidade

---

## 3. (Opcional, mas recomendado) Extension Pack

O Extension Pack adiciona recursos importantes, como:

* USB 2.0/3.0
* Suporte a webcam
* Melhor integração de dispositivos

### Instalação

1. Baixe o **VirtualBox Extension Pack** da mesma versão do VirtualBox
2. Abra o VirtualBox
3. Vá em:

   * Arquivo > Ferramentas > Preferências > Extensões
4. Clique em **Adicionar** e selecione o arquivo `.vbox-extpack`
5. Aceite os termos

---

## 4. Download da VM Kali Linux

### 4.1 Acessar o site oficial

1. Acesse o site oficial do Kali Linux
2. Vá até **Get Kali** ou **Download**
3. Selecione:

   * **Virtual Machines**

### 4.2 Escolher a versão correta

Baixe a opção:

* **Kali Linux VirtualBox Image**

O arquivo normalmente estará no formato:

* `.ova`

> Esse arquivo já vem pré-configurado para uso no VirtualBox.

---

## 5. Importando o Kali Linux no VirtualBox

### 5.1 Importar a VM

1. Abra o VirtualBox
2. Clique em **Arquivo > Importar Appliance**
3. Selecione o arquivo `.ova` do Kali Linux
4. Clique em **Avançar**
5. Revise as configurações
6. Clique em **Importar**

A importação pode levar alguns minutos.

---

## 6. Ajustes Recomendados da Máquina Virtual

Após a importação, antes de iniciar a VM:

1. Selecione a VM **Kali Linux**
2. Clique em **Configurações**

### 6.1 Sistema

* Memória RAM: 4096 MB ou mais
* Processadores: 2 ou mais (se disponível)
* Ordem de boot: Disco rígido em primeiro lugar

### 6.2 Tela

* Memória de vídeo: 128 MB
* Controlador gráfico: VMSVGA

### 6.3 Rede

* Modo padrão: NAT

---

## 7. Iniciando o Kali Linux

1. Selecione a VM **Kali Linux**
2. Clique em **Iniciar**
3. Aguarde o boot do sistema

### Credenciais padrão

* Usuário: `kali`
* Senha: `kali`

---

## 8. Pós-instalação (Recomendado)

### 8.1 Atualizar o sistema

```bash
sudo apt update && sudo apt upgrade -y
```

### 8.2 Instalar Guest Additions

1. Com a VM ligada, vá em:

   * Dispositivos > Inserir imagem de CD dos Adicionais para Convidado
2. Execute no Kali:

```bash
sudo mkdir /media/cdrom
sudo mount /dev/cdrom /media/cdrom
cd /media/cdrom
sudo sh VBoxLinuxAdditions.run
```

3. Reinicie a VM

Isso melhora resolução de tela, desempenho e integração com o host.

---

## 9. Problemas Comuns

### Tela preta ou resolução baixa

* Verifique se o controlador gráfico está como **VMSVGA**
* Instale o Guest Additions

### Erro de virtualização

* Ative VT-x/AMD-V na BIOS
* No Windows, desative Hyper-V, WSL2 e Virtual Machine Platform

---

## 10. Conclusão

Após esses passos, o Kali Linux estará totalmente funcional dentro do VirtualBox, pronto para estudos, laboratórios e práticas de segurança da informação.

---

**Fim do guia**
