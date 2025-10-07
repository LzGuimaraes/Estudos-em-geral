## Nome: Luiz Fernando dos Santos Guimarães

# 1. Qual a diferença entre hub, switch e roteador?

R:

<h2>HUB</h2> é um dispositivo que opera na camada fisíca do modelo OSI.

Sua função é interligar vários computadores em uma rede local. Quando um pacore de dados chega a uma porta do hub, ele é simplesmente copiado e retransmitido para todas as outras portas, sem qualquer analise sobre quem é o remetente ou o distinatário. Ele age como um espelho, replicando a informação para todos. Tem baixa eficiência porque todos os dispositivos recebem todos os dados, e isso gera tráfegode rede desnecessário, oque pode causar lentidão e colisões de dados, tem baixa segurança pelo fado de enviar para todos dispositivos os dados, especialmente em redes maiores.

<h2>Switch</h2> Tem a mesma função do hub porém ele opera na camada de enlace. Ele opera de forma mais inteligente. A grande diferença entre os dois é que o switch consegue identificar o endereço MAC dos dispositivos conectados a cada uma de suas portas.

Quando recebe um pacote de dados, analisa e o encaminha somente para a porte onde o dispositivode destino esta conectado tem alta eficiência por direcionar o tráfego de forma seletiva. O switch reduz o congestionamento da rede, otimiza a largura de banda e melhora o desempenho geral da comunicação entre dispositivos.

<h2>Roteador</h2> É o dispositivo mais sofisticado dos três, operando na camada de rede. Sua principal função não é só conectar dispositivos de uma mesma rede mas também de redes diferentes

Utiliza endereços IP para determinar o melhor caminho para encaminhar os pacotes entre redes diferentes. É ele permite que vários dispositivos compartilhem uma única conexão com a internet.

Os roteadores modernos tem funcionalidades extras, como a criação de uma rede sem fio(Wi-fi), firewall para proteção contra ameaças externas, gerenciamento de tráfego.

Alta segurança com recursos como firewall e a capacidade de criptografar a comunicação, os roteadores são essenciais para proteger a rede interna de acessos não autorizados vindos da internet.

# 2. Descreva a tecnologia Ethernet (objetivos, método de acesso ao meio, padrões entre outros).

R:

<h2>Objetivos Fundamentais da Ethernet</h2>

**Simplicidade e Baixo Custo**: Criar um sistema de rede que fosse fácil de instalar, gerenciar e manter, utilizando componentes de hardware e cabos de baixo custo.

**Interoperabilidade** : Garantir que dispositivos de diferentes fabricantes pudessem se comunicar sem problemas na mesma rede. Isso foi alcançado através de uma padronizaçãoo rigorosa.

**Escalabilidade e Flexibilidade**: Permitir que a rede crescesse facilmente, adicionando novos dispositivos sem grandes reconfigurações. Além disso deveria ser flexível o suficiente para ser usada em diferentes topologias fisícas.

**Desempenho e Confiabilidade**: Oferecer uma comunicação de dados eficiente e confiável, capaz de evoluir para suportar velocidade cada vez maiores conforme a tecnologia avançasse.

<h2>Método de Acesso</h2>

**CSMA/CD**: Conhecido como CSMA/CD(Carrier Sense Multiple Access with Collision Detection) "Acesso Múltiplo com Detecção de Portadora e Detecção de Colisão"

1. **Carrier Sense**: Antes de trasmitir o dados, o dispositvo "escuta" o cabo para verificar se ele está livre, ou seja, se nenhum outro dispositvo está transmitindo no momento.
2. **Multiple Access**: Vários dispositivos estão conectados ao mesmo meio de transmissão e têm a oportunidade de enviar dados.
3. **Collision Detection**: Se dois dispositivos "escutarem"o cabo, o encontrarem livre e decidirem transmitir exatamente ao mesmo tempo, ocorrerá uma colisão. Quando uma colisão é detectada, ambos os dispositivos param a transmissão, enviam um sinal de congestionamento para alertar todos os outros sobre a colisão e, em seguida aguardam um período de tempo aleatório antes de tentarem transmitir novamente.

#### 3. Padrões Ethernet

A Ethernet é padronizada pelo Instituto de Engenheiros Eletricistas e Eletrônicos (IEEE) sob o comitê IEE 802.3. Este padrão define as especificações físicas (cabos, conectores) e de enlace (endereçamento MAC, formato de quadro) da tecnologia. A evolução da Ethernet é marcada por seus padrões de velocidade:

- **Ethernet (10 Mbps)**: O padrão original, usava cabos coaxiais e posteriormente cabos de par trançado.
- **Fast Ethernet (100 Mbps)**: Solidificou o uso de cabos de par trançado e a topologia em estrela com switches.
- **Gigabit Ethernet (1 Gbps ou 1.000 Mbps)**: Tornou-se o padrão para a maioria dos computadores e redes modernas, utilizando cabos de par trançado Permitiu a transmissão de arquivos pesados e streaming de vídeo em alta qualidade com muito mais eficiência.

### 4. Estrutura e Endereçamento

- **Quadro (Frame) Ethernet**: Os dados na Ethernet não são enviados de forma contínua, mas sim divididos em pacotes chamados "quadros" ou "frames". Cada quadro contém, além dos dados úteis, informações de controle essenciais, como:

  - Endereço MAC de Destino e Origem: Um endereço físico de 48 bits, único para cada placa de rede no mundo. O switch usa o endereço MAC de destino para encaminhar o quadro apenas para a porta correta.
  - Tipo (EtherType): Indica qual protocolo de camada superior (como o IP) está contido no quadro.
  - Dados: A informação que está sendo transportada.
  - CRC (Cyclic Redundancy Check): Um código de verificação para detectar erros na transmissão.

- **Topologia**: Embora historicamente a Ethernet tenha usado uma topologia de barramento (com cabo coaxial), a topologia dominante hoje é a estrela. Nela, todos os dispositivos se conectam a um ponto central, geralmente um switch, que gerencia a comunicação de forma eficiente.

# 3.Qual é a diferença entre controle de fluxo e controle de congestionamento do TCP? <br> Explique os dois métodos em detalhes.

R:

1. **Controle de Fluxo (Flow Control)**:
   Controle de fluxo é como uma conversa entre duas pessoas, onde uma fala (transmissor) e a outra escuta (receptor). Se o falante for muito rápito. o ouvinte não conseguirá acompanhar.

- Objetivo: Evitar que o transmissor envie dados mais rápido do que o receptor consegue recebê-los e processálos.

Como funciona? O mecanismo da Janela Deslizante(`rwnd`)

Cada dispositivo que usa TCP possui uma área de memória temporária chamada buffer de recepção.Quando os pacotes chegam, eles são armazenados nesse buffer antes de serem processados pelo aplicativo (por exemplo, um navegador web). O controle de fluxo funciona gerenciando o espaço livre nesse buffer.

1. **Anunciando a Capacidade**: O receptor informa ao transmissor o quanto de espaço livre ele tem em seu buffer. esse valor é chamado de **Janela de recepção**( receive window ou `rmnd`) e é enviado dentro do cabeçalho de cada pacote de confirmação (ACK) que o receptor manda de volta.
2. **Ajuste do Transmissor**: O transmissor mantém um registro do `rwnd` do receptor e garante que a quantidade de dados enviados que ainda não foram confirmados (ACK) nunca exceda esse valor.

Exemplo:

- **Passo 1**: O Receptor A tem um buffer de 64 KB e está vazio. Ele envia um ACK para o Transmissor B dizendo: "Minha `rwnd` é de 64 KB".
- **Passo 2**: O Transmissor B sabe que pode enviar até 64 KB de dados antes de precisar esperar por uma confirmação. Ele envia 30 KB.
- **Passo 3**: O Receptor A recebe os 30 KB e os coloca no buffer. Enquanto isso, seu aplicativo processa 10 KB desses dados, liberando espaço. Agora, o buffer tem 20 KB ocupados e 44 KB livres.
- **Passo 4**: O Receptor A envia um ACK para o Transmissor B confirmando o recebimento dos 30 KB e anunciando seu novo estado: "Recebi os dados, e minha `rwnd` agora é de 44 KB".
- **Passo 5**: O Transmissor B agora sabe que só pode enviar mais 44 KB.

**E se a janela chegar a zero** (`rwnd = 0`)?
Se o buffer do receptor ficar cheio, ele anunciará uma `rwnd` de 0. O transmissor para de enviar dados, mas inicia um "timer de persistência". Quando o timer expira, ele envia um pequeno pacote de "sondagem" para que o receptor anuncie novamente o tamanho da sua janela, evitando um bloqueio permanente.

**2. Controle de Congestionamento (Congestionamento Control)**

O controle de congestionamento é como dirigir em uma rodovia. O limite de velocidade do seu carro (receptor) pode ser alto, mas se houver um engarrafamento (congestionamento na rede), você precisa diminuir a velocidade para não piorar a situação para todos.

**Objetivo**: Evitar que o transmissor injete dados na rede mais rápido do que os roteadores e links intermediários conseguem suportar, prevenindo o colapso da rede (congestion collapse).

**Inferindo o Estado da Rede**

O transmissor não recebe um sinal direto dos roteadores dizendo "a rede está congestionada!". Ele precisa inferir o congestionamento a partir de dois sinais principais: perda de pacotes e aumento no tempo de ida e volta (RTT). Para gerenciar isso, o transmissor mantém uma outra variável de janela, a janela de congestionamento (congestion window ou `cwnd`).

O TCP usa principalmente quatro algoritmos interligados:

1. **Slow Start (Partida Lenta)**: Apesar do nome, esta é uma fase de crescimento exponencial. A conexão começa com uma `cwnd` pequena (geralmente 1 ou 10 MSS - Maximum Segment Size). A cada ACK recebido, a `cwnd` é aumentada, efetivamente dobrando de tamanho a cada RTT. O objetivo é encontrar rapidamente a capacidade disponível da rede.
2. **Congestion Avoidance (Prevenção de Congestionamento)**: Quando a `cwnd` atinge um limiar (chamado ssthresh), o crescimento exponencial para e se torna linear (aditivo). A `cwnd` aumenta em apenas 1 MSS por RTT. O objetivo é sondar cuidadosamente por mais banda disponível sem causar congestionamento.
3. **Fast Retransmit (Retransmissão Rápida)**: Se o transmissor recebe três ACKs duplicados (ou seja, quatro ACKs para o mesmo segmento), ele assume que o segmento seguinte foi perdido e o retransmite imediatamente, sem esperar o timer de retransmissão estourar. Isso indica um congestionamento leve.
4. **Fast Recovery (Recuperação Rápida)**: Após um Fast Retransmit, o TCP não volta para o Slow Start. Em vez disso, ele corta a `cwnd` pela metade (diminuição multiplicativa) e entra diretamente na fase de Prevenção de Congestionamento.

A reação à perda de pacotes é a chave:

- **Perda detectada por 3 ACKs duplicados (congestionamento leve)**: A cwnd é reduzida pela metade.

- **Perda detectada por timeout (congestionamento severo)**: A `cwnd` é drasticamente reduzida para 1 MSS, e o processo recomeça do Slow Start.

No geral:

<div align="center">

## 📊 Tabela Comparativa

<table style="border-collapse: collapse; width: 100%; margin: 20px 0;">
<tr style="background-color: #2c3e50; color: white;">
<th style="padding: 12px; border: 1px solid #ddd;color: #e74c3c;">Característica</th>
<th style="padding: 12px; border: 1px solid #ddd;color: #e74c3c;">Controle de Fluxo</th>
<th style="padding: 12px; border: 1px solid #ddd;color: #e74c3c;">Controle de Congestionamento</th>
</tr>
<tr style="background-color: #f8f9fa;">
<td style="padding: 10px; border: 1px solid #ddd;color: #5133d6ff;"><strong>Objetivo Principal</strong></td>
<td style="padding: 10px; border: 1px solid #ddd;color: #5133d6ff;">Proteger o receptor</td>
<td style="padding: 10px; border: 1px solid #ddd;color: #5133d6ff;">Proteger a rede</td>
</tr>
<tr style="background-color: #f8f9fa;">
<td style="padding: 10px; border: 1px solid #ddd;color: #e74c3c;"><strong>Problema a Resolver</strong></td>
<td style="padding: 10px; border: 1px solid #ddd;color: #e74c3c;">Transmissor muito rápido para o receptor</td>
<td style="padding: 10px; border: 1px solid #ddd;color: #e74c3c;">Muitos transmissores sobrecarregando a rede</td>
</tr>
<tr style="background-color: #f8f9fa;">
<td style="padding: 10px; border: 1px solid #ddd;color: #5133d6ff;"><strong>Mecanismo Chave</strong></td>
<td style="padding: 10px; border: 1px solid #ddd;color: #5133d6ff;">Janela de Recepção (rwnd)</td>
<td style="padding: 10px; border: 1px solid #ddd;color: #5133d6ff;">Janela de Congestionamento (cwnd)</td>
</tr>
<tr style="background-color: #f8f9fa;">
<td style="padding: 10px; border: 1px solid #ddd;color: #e74c3c;"><strong>Quem Controla</strong></td>
<td style="padding: 10px; border: 1px solid #ddd;color: #e74c3c;">O receptor controla, anunciando sua rwnd</td>
<td style="padding: 10px; border: 1px solid #ddd;color: #e74c3c;">O transmissor controla, ajustando sua cwnd</td>
</tr>
<tr style="background-color: #f8f9fa;">
<td style="padding: 10px; border: 1px solid #ddd;color: #5133d6ff;"><strong>Comunicação</strong></td>
<td style="padding: 10px; border: 1px solid #ddd;color: #5133d6ff;">Explícita (receptor informa sua capacidade)</td>
<td style="padding: 10px; border: 1px solid #ddd;color: #5133d6ff;">Implícita (transmissor infere o estado da rede)</td>
</tr>
</table>

</div>

O transmissor TCP está sempre sujeito a ambas as janelas. A quantidade real de dados que ele pode enviar sem receber uma confirmação é o mínimo entre a janela de recepção e a janela de congestionamento.

<p align="center" style="color: #5133d6ff; font-weight: bold; font-size: 1.1em;">
Dados a Enviar < min(rwnd, cwnd)
</p>

Isso garante que a transmissão respeite tanto a capacidade do receptor de processar os dados quanto a capacidade da rede de transportá-los, tornando o TCP um protocolo robusto e adaptável para a internet.

# 4. Considerar o endereço IP 200.134.9.2/20 de uma sub­rede com máscara de acordo com a notação CIDR. Apresentar a faixa total de endereços atribuída a essa sub­rede, incluindo: endereço de rede, primeiro endereço de máquina, último endereço de máquina e o endereço de broadcast.

R:

**Máscara de Rede CIDR (/20)**
A notação `/20` significa que os primeiros **20 bits** do endereço IP de 32 bits são usados para identificar a **rede** e os restantes são para identificar as máquinas dentro da rede.

- Total de bits em um endereçamento IP: 32 bits
- Bits de rede 20
- Bits de Host: 12 bits.

2. Máscara de rede em formato binário e decimal:

** Máscara em Binário**:
20 bits = `11111111.11111111.11110000.00000000`

**Máscara em Decimal**: Convertendo cada octeto do binário para decimal:

- `11111111` = 255

- `11111111` = 255

- `11110000` = 240

- `00000000` = 0

Portanto, a máscara de sub-rede é `255.255.240.0`

**Converter o Endereço IP para Binário**

IP original: (`200.134.9.2`) conversão para binário

- 200 = 11001000
- 134 = 10000110
- 9 = 00001001
- 2 = 00000010

O endereço IP completo em binário é:

- `11001000.10000110.00001001.00000010`

**Encontrar o Endereço de Rede**

```
IP:      11001000.10000110.00001001.00000010  (200.134.9.2)
Máscara:   11111111.11111111.11110000.00000000  (255.255.240.0)
----------------------------------------------------------- (Operação E)
  Rede:    11001000.10000110.00000000.00000000
```

Conversão para decimal:

- 11001000 = 200
- 10000110 = 134
- 00000000 = 0
- 00000000 = 0

Endereço de Rede: `200.134.0.0`

**Endereço de Broadcast**

Troca de todos os bits da porção de host (os últimos 12) para `1`.

Endereço de Rede (binário): `11001000.10000110.00000000.00000000`

Parte da Rede (primeiros 20 bits): `11001000.10000110.0000`

Parte do Host (últimos 12 bits): `0000.00000000`

Trocando os bits do host para `1`: `1111.11111111`

Juntando as partes:
`11001000.10000110.00001111.11111111`

Convertendo este resultado para decimal

- `11001000` = `200`
- `10000110` = `134`
- `00001111` = `15`
- `11111111` = `255`

Endereço de Broadcast: `200.134.15.255`

**Determinar a Faixa de Endereços de Máquina**

Os endereços que podemos atribuir aos nossos dispositivos (computadores, servidores, etc.) estão entre o endereço de rede e o de broadcast.

**Primeiro Endereço de Máquina**: É o endereço de rede + 1. `200.134.0.0` + 1 = `200.134.0.1`
**Último Endereço de Máquina**: É o endereço de broadcast - 1. `200.134.15.255` - 1 = `200.134.15.254`

- **Endereço de Rede**: 200.134.0.0

- **Primeiro Endereço de Máquina**: 200.134.0.1

- **Último Endereço de Máquina**: 200.134.15.254

- **Endereço de Broadcast**: 200.134.15.255

# 5. Dê um exemplo completo do funcionamento do NAT mostrando o que muda nos pacotes de saída e entrada da rede local, tabela NAT, etc.

### Rede Local (LAN):

    - **Seu Computador**: Tem o endereço IP privado `192.168.1.10`.
    - Seu Roteador: Tem um IP privado `192.168.1.1` na rede local e um IP público, fornecido pelo seu provedor, que é `203.0.113.50`.
    -

### A Internet (WAN):

    - Servidor Web (ex: www.google.com): Tem o endereço IP público `8.8.8.8`.

**Objetivo:**: Seu computador (192.168.1.10) quer acessar o site no servidor (8.8.8.8).

O Pacote de Saída (Da sua rede para a Internet)

**Passo 1**: Seu computador cria o pacote

Seu computador quer fazer uma requisição web (HTTP), que usa a porta 80. Ele cria um pacote IP com as seguintes informações no cabeçalho:

### 📊 Cabeçalho TCP/IP - Exemplo de Conexão HTTP

<div align="center">

<table style="border-collapse: collapse; width: 80%; margin: 20px 0; box-shadow: 0 2px 8px rgba(0,0,0,0.1);">
<tr style="background-color: #2c3e50; color: white;">
<th style="padding: 15px; border: 1px solid #ddd; text-align: center;">Cabeçalho</th>
<th style="padding: 15px; border: 1px solid #ddd; text-align: center;">Valor</th>
<th style="padding: 15px; border: 1px solid #ddd; text-align: center;">Descrição</th>
</tr>
<tr style="background-color: #f8f9fa;">
<td style="padding: 12px; border: 1px solid #ddd; font-weight: bold;color: #e74c3c;">IP de Origem</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #e74c3c;">192.168.1.10</td>
<td style="padding: 12px; border: 1px solid #ddd;color: #e74c3c;">O IP do seu PC (privado)</td>
</tr>
<tr style="background-color: #f8f9fa;">
<td style="padding: 12px; border: 1px solid #ddd; font-weight: bold;color: #e74c3c;">Porta de Origem</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #e74c3c;">50000</td>
<td style="padding: 12px; border: 1px solid #ddd;color: #e74c3c;">Uma porta aleatória e temporária</td>
</tr>
<tr style="background-color: #f8f9fa;">
<td style="padding: 12px; border: 1px solid #ddd; font-weight: bold;color: #2980b9;">IP de Destino</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #2980b9;">8.8.8.8</td>
<td style="padding: 12px; border: 1px solid #ddd;color: #2980b9;">O IP do servidor do Google (público)</td>
</tr>
<tr style="background-color: #f8f9fa;">
<td style="padding: 12px; border: 1px solid #ddd; font-weight: bold; color: #2980b9;">Porta de Destino</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #2980b9;">80</td>
<td style="padding: 12px; border: 1px solid #ddd;color: #2980b9;">Porta padrão para HTTP</td>
</tr>
</table>
</div>

Seu computador envia este pacote para o gateway padrão da rede, que é o seu roteador.

**Passo 2: O Roteador faz a "mágica" do NAT**

O roteador recebe o pacote e percebe que ele veio de um endereço IP privado (192.168.1.10) e que precisa ser enviado para a internet. Endereços privados não são roteáveis na internet, então o roteador precisa "traduzir" o endereço.

**Modifica o Pacote**: O roteador reescreve o cabeçalho do pacote

- Troca o IP de Origem privado (192.168.1.10) pelo seu próprio IP público (203.0.113.50).
- Troca a Porta de Origem original (50000) por uma nova porta pública disponível para ele, por exemplo, 12345.

**Cria uma Entrada na Tabela NAT**: O roteador precisa se lembrar dessa tradução para saber para quem entregar a resposta quando ela chegar. Ele cria uma entrada em sua tabela NAT (também chamada de tabela de tradução):

**Tabela NAT do Roteador**:

### 🌐 Tabela de Tradução NAT (Network Address Translation)

<div align="center">

<table style="border-collapse: collapse; width: 90%; margin: 20px 0; box-shadow: 0 2px 8px rgba(0,0,0,0.1);">
<tr style="background-color: #34495e; color: white;">
<th style="padding: 15px; border: 1px solid #ddd; text-align: center;">IP Privado Origem</th>
<th style="padding: 15px; border: 1px solid #ddd; text-align: center;">Porta Privada</th>
<th style="padding: 15px; border: 1px solid #ddd; text-align: center;">IP Público (NAT)</th>
<th style="padding: 15px; border: 1px solid #ddd; text-align: center;">Porta Pública (NAT)</th>
<th style="padding: 15px; border: 1px solid #ddd; text-align: center;">IP Destino</th>
</tr>
<tr style="background-color: #f8f9fa;">
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #e74c3c; text-align: center;">192.168.1.10</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #e74c3c; text-align: center;">50000</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #2980b9; text-align: center;">203.0.113.50</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #2980b9; text-align: center;">12345</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #27ae60; text-align: center;">8.8.8.8</td>
</tr>
</table>

</div>

**Passo: O pacote modificado viaja pela Internet**

O roteador agora envia o pacote modificado para a internet. Para o servidor do Google, a requisição parece ter vindo diretamente do seu roteador.

O pacote que chega no servidor do Google tem as seguintes informações:

### 🌐 Cabeçalho TCP/IP Após NAT

<div align="center">

<table style="border-collapse: collapse; width: 70%; margin: 20px 0; box-shadow: 0 2px 8px rgba(0,0,0,0.1);">
<tr style="background-color: #2c3e50; color: white;">
<th style="padding: 15px; border: 1px solid #ddd; text-align: left;">Cabeçalho</th>
<th style="padding: 15px; border: 1px solid #ddd; text-align: left;">Valor</th>
</tr>
<tr style="background-color: #f8f9fa;">
<td style="padding: 12px; border: 1px solid #ddd; font-weight: bold; color: #e74c3c;">IP de Origem</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #e74c3c;">
203.0.113.50 <span style="color: #7f8c8d; font-style: italic;">(MODIFICADO)</span>
</td>
</tr>
<tr style="background-color: #ffffff;">
<td style="padding: 12px; border: 1px solid #ddd; font-weight: bold; color: #e74c3c">Porta de Origem</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #e74c3c;">
12345 <span style="color: #7f8c8d; font-style: italic;">(MODIFICADO)</span>
</td>
</tr>
<tr style="background-color: #f8f9fa;">
<td style="padding: 12px; border: 1px solid #ddd; font-weight: bold; color: #2980b9;">IP de Destino</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #2980b9;">8.8.8.8</td>
</tr>
<tr style="background-color: #ffffff;">
<td style="padding: 12px; border: 1px solid #ddd; font-weight: bold; color: #2980b9;">Porta de Destino</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #2980b9;">80</td>
</tr>
</table>
</div>

Para o seu computador, a comunicação parece ter sido direta com o servidor do Google. Ele não tem nenhuma ciência de que o roteador fez todo esse trabalho de tradução nos bastidores.

## O Pacote de Entrada (A resposta da Internet para sua rede)

**Passo 4: O servidor responde**

O servidor do Google processa a requisição e envia um pacote de resposta. Ele inverte as informações de origem e destino que recebeu:

### 🌐 Resposta do Servidor - Cabeçalho TCP/IP

<div align="center">

<table style="border-collapse: collapse; width: 70%; margin: 20px 0; box-shadow: 0 2px 8px rgba(0,0,0,0.1);">
<tr style="background-color: #2c3e50; color: white;">
<th style="padding: 15px; border: 1px solid #ddd; text-align: left;">Cabeçalho</th>
<th style="padding: 15px; border: 1px solid #ddd; text-align: left;">Valor</th>
</tr>
<tr style="background-color: #f8f9fa;">
<td style="padding: 12px; border: 1px solid #ddd; font-weight: bold; color: #2980b9;">IP de Origem</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #2980b9;">8.8.8.8</td>
</tr>
<tr style="background-color: #ffffff;">
<td style="padding: 12px; border: 1px solid #ddd; font-weight: bold; color: #2980b9;">Porta de Origem</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #2980b9;">80</td>
</tr>
<tr style="background-color: #f8f9fa;">
<td style="padding: 12px; border: 1px solid #ddd; font-weight: bold; color: #e74c3c;">IP de Destino</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #e74c3c;">203.0.113.50</td>
</tr>
<tr style="background-color: #ffffff;">
<td style="padding: 12px; border: 1px solid #ddd; font-weight: bold; color: #e74c3c;">Porta de Destino</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #e74c3c;">12345</td>
</tr>
</table>
</div>

Este pacote de resposta viaja pela internet e chega na interface pública do seu roteador.

**Passo 5: O roteador faz a tradução reversa**

- **Consulta a Tabela NAT**: O roteador procura em sua tabela NAT uma entrada que corresponda à porta `12345`. Ele encontra a linha que criamos no Passo 2.

### 🌐 Tabela NAT do Roteador

<div align="center">

<table style="border-collapse: collapse; width: 95%; margin: 20px 0; box-shadow: 0 2px 8px rgba(0,0,0,0.1);">
<tr style="background-color: #34495e; color: white;">
<th style="padding: 15px; border: 1px solid #ddd; text-align: center;">IP Privado Origem</th>
<th style="padding: 15px; border: 1px solid #ddd; text-align: center;">Porta Privada</th>
<th style="padding: 15px; border: 1px solid #ddd; text-align: center;">IP Público (NAT)</th>
<th style="padding: 15px; border: 1px solid #ddd; text-align: center;">Porta Pública (NAT)</th>
<th style="padding: 15px; border: 1px solid #ddd; text-align: center;">IP Destino</th>
</tr>
<tr style="background-color: #f8f9fa;">
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #e74c3c; text-align: center; font-weight: bold;">192.168.1.10</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #e74c3c; text-align: center; font-weight: bold;">50000</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #2980b9; text-align: center; font-weight: bold;">203.0.113.50</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #2980b9; text-align: center; font-weight: bold;">12345</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #27ae60; text-align: center; font-weight: bold;">8.8.8.8</td>
</tr>
</table>
</div>

- **Modifica o Pacote de Resposta**: Usando as informações da tabela, ele traduz o pacote de volta para a linguagem da rede local:
  - Troca o IP de Destino (`203.0.113.50`) pelo IP privado original (`192.168.1.10`).
  - Troca a Porta de Destino (`12345`) pela porta privada original (`50000`).

**Passo 6: O pacote chega ao seu computador**

O roteador envia o pacote final, agora traduzido, para o seu computador. O pacote que o seu PC recebe é este:

### 🌐 Pacote Retraduzido pelo Roteador

<div align="center">

<table style="border-collapse: collapse; width: 70%; margin: 20px 0; box-shadow: 0 2px 8px rgba(0,0,0,0.1);">
<tr style="background-color: #2c3e50; color: white;">
<th style="padding: 15px; border: 1px solid #ddd; text-align: left;">Cabeçalho</th>
<th style="padding: 15px; border: 1px solid #ddd; text-align: left;">Valor</th>
</tr>
<tr style="background-color: #f8f9fa;">
<td style="padding: 12px; border: 1px solid #ddd; font-weight: bold;color: #2980b9;">IP de Origem</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #2980b9;">8.8.8.8</td>
</tr>
<tr style="background-color: #ffffff;">
<td style="padding: 12px; border: 1px solid #ddd; font-weight: bold;color: #2980b9;">Porta de Origem</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #2980b9;">80</td>
</tr>
<tr style="background-color: #f8f9fa;">
<td style="padding: 12px; border: 1px solid #ddd; font-weight: bold; color: #e74c3c;">IP de Destino</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #e74c3c;">
192.168.1.10 <span style="color: #7f8c8d; font-style: italic;">(MODIFICADO)</span>
</td>
</tr>
<tr style="background-color: #ffffff;">
<td style="padding: 12px; border: 1px solid #ddd; font-weight: bold; color: #e74c3c;">Porta de Destino</td>
<td style="padding: 12px; border: 1px solid #ddd; font-family: monospace; color: #e74c3c;">
50000 <span style="color: #7f8c8d; font-style: italic;">(MODIFICADO)</span>
</td>
</tr>
</table>
</div>

Para o seu computador, a comunicação parece ter sido direta com o servidor do Google. Ele não tem nenhuma ciência de que o roteador fez todo esse trabalho de tradução nos bastidores.

**Resumo do Processo**
**- Na Saída**: O NAT altera o **endereço de origem** (IP e Porta) do pacote, registrando a mudança na Tabela NAT.

**- Na Entrada**: O NAT consulta a Tabela NAT usando a porta de destino da resposta, descobre qual dispositivo interno fez a requisição e altera o endereço de destino (IP e Porta) do pacote antes de entregá-lo.
