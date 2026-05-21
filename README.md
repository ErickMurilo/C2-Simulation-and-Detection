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
