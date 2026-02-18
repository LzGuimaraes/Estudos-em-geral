# Redirecionamento de Portas em CFTV – Guia Prático

Este documento explica, de forma objetiva, como funciona o redirecionamento de portas (port forwarding) em cenários de CFTV, com foco nas portas mais comuns: 8080 → 80, 554 (RTSP) e 37777 (porta de serviço).

---

## 1. Conceito fundamental

Em redes com NAT, o roteador recebe conexões no IP público e decide para qual equipamento interno (IP privado) e porta interna essa conexão deve ser enviada.

A regra sempre segue o padrão:

PORTA PÚBLICA → IP INTERNO : PORTA INTERNA

Não existe redirecionamento “por IP inteiro”. O redirecionamento é sempre feito porta a porta.

---

## 2. Exemplo base do cenário

Equipamento (DVR/NVR):

IP interno: 192.168.1.50

Portas do equipamento:

- Web (HTTP): 80
- RTSP (stream de vídeo): 554
- Porta de serviço (aplicativo / CMS): 37777

IP público do local:
187.xxx.xxx.xxx

---

## 3. Por que aparece 8080 fora e 80 dentro

É comum mapear assim:

Porta pública: 8080
IP interno: 192.168.1.50
Porta interna: 80

Isso significa que, quando alguém acessa:

[http://187.xxx.xxx.xxx:8080](http://187.xxx.xxx.xxx:8080)

O roteador entrega a conexão para:

[http://192.168.1.50:80](http://192.168.1.50:80)

A porta 8080 é apenas uma escolha externa. Ela não precisa ser igual à porta interna. Muitas vezes a porta 80 externa já está em uso pelo próprio roteador.

---

## 4. Porta 554 – RTSP

A porta 554 é usada para o serviço de streaming RTSP.

Na maioria dos DVRs e NVRs:

RTSP = 554

Regra típica:

Porta pública 554 → 192.168.1.50 : 554

Ou, se desejar mudar a porta externa:

Porta pública 10554 → 192.168.1.50 : 554

No cliente de vídeo (VLC, CMS, NVR remoto, etc.), o acesso passa a usar a porta pública configurada.

---

## 5. De onde vem a porta 37777

A porta 37777 não é padrão de rede nem padrão da Internet.

Ela é uma porta definida pelo fabricante do equipamento para o serviço principal de comunicação com:

- software cliente
- aplicativo mobile
- CMS

Essa porta é usada para protocolo proprietário do fabricante.

No menu do equipamento normalmente aparece como:

- TCP Port
- Server Port
- Service Port
- Media Port (em alguns modelos)

Exemplo comum no equipamento:

HTTP Port: 80
RTSP Port: 554
TCP Port: 37777

---

## 6. Como funciona o redirecionamento um a um

Cada porta precisa de uma regra independente no roteador.

Para o exemplo do DVR 192.168.1.50:

Regra 1 – Web
Porta pública 8080 → 192.168.1.50 : 80

Regra 2 – RTSP
Porta pública 554 → 192.168.1.50 : 554

Regra 3 – Aplicativo / CMS
Porta pública 37777 → 192.168.1.50 : 37777

Essas regras não se misturam. Cada uma é tratada separadamente pelo roteador.

---

## 7. Limitação importante

Uma mesma porta pública só pode apontar para um único destino interno.

Exemplo que NÃO é possível:

Porta pública 554 → câmera 1
Porta pública 554 → câmera 2

Para acessar dois equipamentos diferentes usando RTSP externamente, é necessário usar portas públicas diferentes:

554 → 192.168.1.50 : 554
5554 → 192.168.1.51 : 554

---

## 8. Regra mental para campo

Sempre pense da seguinte forma:

Qual porta externa vai receber a conexão?
Para qual IP interno essa conexão deve ir?
Em qual porta interna o serviço está escutando?

---

## 9. Observação operacional (NOC / CFTV)

Abrir portas como 554 e 37777 diretamente para a Internet expõe o equipamento.

Sempre que possível, o acesso deve ser feito via VPN, evitando a publicação direta das portas do DVR ou NVR.
