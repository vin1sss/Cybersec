# Guia Completo de Instalação

Este guia descreve como:

* Instalar o VirtualBox
* Baixar uma máquina virtual (VM) do Kali Linux
* Importar e executar o Kali Linux no VirtualBox

---

## Instalação do VirtualBox

1. Acesse o site oficial do VirtualBox: https://www.virtualbox.org/
<img width="1839" height="862" alt="image" src="https://github.com/user-attachments/assets/b24e76c1-cc6d-4761-9582-dee45e0302da" />

<p>  </p>

2. Vá até a seção **Downloads**
<img width="1843" height="867" alt="image" src="https://github.com/user-attachments/assets/d2beeab3-10bc-4f6e-abf8-927e70313ef3" />


3. Baixe a versão correspondente ao seu sistema operacional:

   * Windows hosts
   * macOS hosts
   * Linux distributions

    <img width="414" height="229" alt="image" src="https://github.com/user-attachments/assets/6150b8b3-0905-4d23-b749-bc72490335d5" />

4. Execute o instalador `.exe`

    <img width="265" height="85" alt="image" src="https://github.com/user-attachments/assets/ac5c57d8-ae2f-47b6-9867-3933b8d0754a" />

5. Avance mantendo as opções padrão
6. Confirme a instalação de drivers de rede quando solicitado
7. Conclua a instalação

## Download da VM Kali Linux

1. Acesse o site oficial do Kali Linux: https://www.kali.org/

<img width="1739" height="873" alt="image" src="https://github.com/user-attachments/assets/ca2d2085-4cc3-4fd4-9b9a-4ae995e995f7" />

<p>  </p>

2. Vá até **Get Kali**

<img width="1725" height="899" alt="image" src="https://github.com/user-attachments/assets/21103478-f747-4f57-be5c-1a678d5458d6" />

<p>  </p>

3. Selecione **Virtual Machines**

<img width="1671" height="899" alt="image" src="https://github.com/user-attachments/assets/12090722-828e-48b7-a2d8-1dd7aba13603" />

<p>  </p>

4. Baixe a opção **VirtualBox**

<img width="318" height="325" alt="image" src="https://github.com/user-attachments/assets/a272f6b8-6fec-4d81-8536-e7d26aeedb06" />

<p>  </p>

5. A pasta normalmente estará compactada, extraia

<img width="302" height="106" alt="image" src="https://github.com/user-attachments/assets/23ccc202-20a9-4746-894e-61b40d24ef41" />

<p>  </p>

<img width="257" height="55" alt="image" src="https://github.com/user-attachments/assets/d688d8e1-117c-4bb5-adb5-3044ad6d8c43" />


## Importando o Kali Linux no VirtualBox

1. Abra o VirtualBox

<img width="1017" height="663" alt="image" src="https://github.com/user-attachments/assets/4e6a2b21-e20c-43e8-adf2-d004046d3ffe" />

<p>  </p>

2. Clique em **Open** e selecione este arquivo na pasta baixada

<img width="971" height="565" alt="image" src="https://github.com/user-attachments/assets/c94cc471-162e-4864-953e-c094f4585c6c" />

<p>  </p>

3. Clique em **Abrir**

<img width="803" height="320" alt="image" src="https://github.com/user-attachments/assets/899e371d-e8fc-4c71-a416-c37401c2028a" />

---

## Ajustes Recomendados da Máquina Virtual

Após a importação, antes de iniciar a VM:

1. Selecione a VM **Kali Linux**
2. Clique em **Configurações**

* Memória RAM: 4096 MB ou mais (se disponível)

<img width="892" height="514" alt="image" src="https://github.com/user-attachments/assets/3b9f53b0-3f19-4da4-ab0f-6e69e7036433" />

<p>  </p>

* Processadores: 2 ou mais (se disponível)

<img width="894" height="512" alt="image" src="https://github.com/user-attachments/assets/b992fcec-6093-4df2-bb67-af58572c4a00" />

---

## Iniciando o Kali Linux

1. Selecione a VM **Kali Linux**
2. Clique em **Iniciar**
3. Aguarde o boot do sistema

### Credenciais padrão

* Usuário: `kali`
* Senha: `kali`

<img width="876" height="728" alt="image" src="https://github.com/user-attachments/assets/762b7c94-ce0f-480c-bb72-32ff3afd81ac" />

<p>  </p>

## Atualizar o Sistema (Recomendado)

1. Execute este comando no terminal, a senha da VM será solicitada
```bash
sudo apt update && sudo apt upgrade -y
```

<img width="1015" height="342" alt="image" src="https://github.com/user-attachments/assets/b4543eb6-832c-4fb5-9454-57ed08a6e87a" />

Este processo demora um bom tempo para terminar

---

## Conclusão

Após esses passos, o Kali Linux estará totalmente funcional dentro do VirtualBox, pronto para estudos, laboratórios e práticas de segurança da informação.

---
