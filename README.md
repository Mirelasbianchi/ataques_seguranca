# Relatório Técnico – Análise de Segurança em Solução IoT (ESP32 Web Server)

## 1. Introdução

Este relatório apresenta a análise de segurança de uma solução IoT baseada em ESP32 que implementa um servidor web local. O objetivo é identificar vulnerabilidades no código-fonte, descrever possíveis ataques e avaliar os riscos associados ao uso dessa solução em um ambiente real.

O código analisado foi baseado no tutorial “ESP32 Web Server – Arduino IDE” (Random Nerd Tutorials).

---

## 2. Análise Estática do Código (Vulnerabilidades)

Após a análise do código-fonte, foram identificadas as seguintes vulnerabilidades:

### 2.1 Falta de Autenticação
O servidor Web do ESP32 não possui nenhum mecanismo de login ou autenticação. Qualquer dispositivo conectado à mesma rede pode acessar o endereço IP do ESP32 e controlar os GPIOs.

**Risco:** Controle não autorizado de dispositivos físicos.

### 2.2 Comunicação Sem Criptografia (HTTP)
A comunicação entre o navegador e o ESP32 é feita via HTTP, sem criptografia (HTTPS).

**Risco:** Os dados podem ser interceptados por terceiros na mesma rede.

### 2.3 Credenciais Wi-Fi em Texto Claro
O SSID e a senha da rede Wi-Fi são armazenados diretamente no código-fonte.

**Risco:** Caso o código seja compartilhado, as credenciais da rede podem ser expostas.

### 2.4 Ausência de Controle de Sessão
O sistema não diferencia usuários autenticados de visitantes, pois não existe sessão nem controle de permissões.

**Risco:** Não há controle de quem está operando o sistema.

---

## 3. Possíveis Ataques Identificados

Foram selecionados dois ataques principais para análise:

1. **Acesso não autorizado via rede local**
2. **Interceptação de tráfego (Sniffing / Man-in-the-Middle)**

---

## 4. Ataque 1 – Acesso Não Autorizado via Rede Local

### Descrição

O atacante acessa o servidor do ESP32 simplesmente digitando o endereço IP no navegador, sem necessidade de autenticação.

### Passo a passo do ataque

1. O atacante se conecta à mesma rede Wi-Fi que o ESP32.
2. Escaneia a rede utilizando ferramentas como `nmap` ou aplicativos de scanner de IP.
3. Identifica o IP do ESP32.
4. Abre o navegador e acessa o IP encontrado.
5. Controla os botões da interface web e aciona os GPIOs.

### Probabilidade de ocorrer

**Alta** — redes Wi-Fi locais geralmente possuem múltiplos usuários.

### Impacto

**Alto** — permite o controle indevido de dispositivos físicos (relés, fechaduras, motores, etc).

### Risco resultante

**Crítico** — alta probabilidade e alto impacto.

---

## 5. Ataque 2 – Interceptação de Tráfego (Sniffing / MITM)

### Descrição

Um atacante captura os pacotes de dados transmitidos entre o navegador e o ESP32.

### Passo a passo do ataque

1. O atacante conecta-se à mesma rede Wi-Fi.
2. Utiliza ferramentas como Wireshark ou tcpdump.
3. Captura os pacotes HTTP transmitidos.
4. Visualiza as requisições GET contendo os comandos de acionamento (ex: `/26/on`).

### Probabilidade de ocorrer

**Média** — exige maior conhecimento técnico.

### Impacto

**Alto** — possibilita copiar os comandos e reproduzir ataques posteriormente.

### Risco resultante

**Alto**.

---

## 6. Tabela Consolidada de Ataques (Ordenada por Risco)

| Título do Ataque                                 | Probabilidade | Impacto | Risco   |
|--------------------------------------------------|---------------|---------|---------|
| Acesso não autorizado via rede local             | Alta          | Alto    | Crítico |
| Interceptação de tráfego (Sniffing / MITM)       | Média         | Alto    | Alto    |

---

## 7. (Ir além) Análise Dinâmica – Teste Prático de Ataque no Sistema 
