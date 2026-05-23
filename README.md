# C2-Simulation


Projeto de simulação de comunicação Command and Control (C2) utilizando PowerShell e análise de tráfego TCP no Wireshark.

O laboratório foi estruturado utilizando rede segmentada entre máquina atacante e host simulado, permitindo monitoramento detalhado do comportamento de beaconing e falhas de conexão TCP.

## Análises Realizadas
- Beaconing TCP
- SYN Flood Behavior
- TCP Handshake Failure
- RST/ACK Analysis
- Port Reuse Detection
- TCP Flags Inspection
- Hex Dump Analysis
- IOC Identification

## Ambiente
- Windows Host
- Kali Linux Virtual Machine
- Internal Network (intnet)
- Wireshark
- PowerShell
- Python HTTP Server

## Objetivo
Demonstrar como comportamentos de beaconing e tentativas de comunicação C2 podem ser identificados através da análise detalhada da camada TCP.


## Análise Detalhada de Tráfego

O relatório completo contendo a análise de pacotes TCP, identificação de comportamento de beaconing, análise de conexões SYN/RST, inspeção hexadecimal e indicadores de comunicação Command and Control (C2) pode ser acessado no link abaixo:

[Acesse o Relatório de Análise de Tráfego C2](c2-beaconing-analysis.md)

[Clique aqui para baixar o arquivo de captura original (PCAPNG)](c2-traffic.pcapng)
