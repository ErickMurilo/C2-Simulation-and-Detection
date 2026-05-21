## Ambiente do Lab

<img width="488" height="287" alt="adaptador1" src="https://github.com/user-attachments/assets/e45ceda5-a5e6-4abd-8351-aac427f6e990" />

<img width="482" height="274" alt="adaptador 2" src="https://github.com/user-attachments/assets/f72c8a37-3632-4b04-bbe1-47fad627c9b1" />

Para a execução de testes com payload baseado em Invoke-WebRequest , a máquina foi estruturada com duas placas de rede. 
A primeira interface atua em modo NAT para manter serviços essenciais, enquanto a segunda foi alocada em uma rede interna (itnet).Essa separação é fundamental pois permite analise de tráfego sem ruído local na rede.

<img width="841" height="181" alt="servidor porta 8080  recorte" src="https://github.com/user-attachments/assets/50e83f55-0e70-4351-b218-3c2e0d37c5aa" />

Foi utilizado o kali linux para simular a máquina do atacante. É necessário identificar o endereço ip da rede (192.168.56.20) e em seguida iniciar o servidor http simples usando python na porta 8080. Este servidor atuará como o ponto central para receber conexões (beacons) vindas da máquina Windons infectada.

<img width="612" height="164" alt="ip windons" src="https://github.com/user-attachments/assets/db1bcd0e-f6eb-4865-a001-e83a28d9650a" />

Identificado e configurado o IPv4 do Windons do adaptador 2 de rede

## Simulação C2

<img width="316" height="69" alt="geramento de trafego c2" src="https://github.com/user-attachments/assets/fe0004a8-46d9-4a78-a556-1f119a1d70f7" />

Na máquina infectada, foi utilizado um script em PowerShell para simular o comportamento do malware. O comando Invoke - WebRequest realiza a chamada ao servidor C2, enquanto o Start - Sleep - Seconds 10 define o intervalo de check - in . Este loop infinito garante que a persistência da comunicação seja mantida, enviando um sinal ao atacante a cada 10 segundos.

## Captura TCP
<img width="743" height="128" alt="image" src="https://github.com/user-attachments/assets/55be560b-9cd9-441a-b5b3-2e4ac06268da" />

Utilizando o filtro ip.dst == 192.168.56.20 , faz a filtragem dos pacotes recebidos pela máquina Windons (origem 192.168.56.10) que está tentando iniciar coneões ([SYN]) sem parar.

O grande volume de alertas em vermelho com o aviso [tcp port numbers reused] é a prova visual do ataque , o  script em PowerShell gerou requisições tão rápidas e frequentes que o sistema operacional esgotou as portas de redes disponíveis sendo obrigado a reutilizá-las.

## Análise SYN

<img width="1112" height="116" alt="ip  recorte" src="https://github.com/user-attachments/assets/cc3d44a4-8197-4d2c-8c75-3f600ebad99d" />

Utilizei o filtro combinado de endereços, isolei todo o tráfego de ida e volta entre a vítima (192.168.56.10) e o atacante (192.168.56.20).

O pedido ([SYN]) mostra a máquina Windons tentando iniciar o aperto de mão tcp na porta 80.
([RST,ACK]) como servidor python no kali linux está ouvindo apenas na porta 8080 e não na porta 80, o sistema operacional responde imediatamanete com um flag de reset (RST), fechando a conexão de forma abrupta por não haver nenhum serviço ativo naquela porta.

# RST/ACK

<img width="1105" height="159" alt="tcp flags recorte" src="https://github.com/user-attachments/assets/2ac31b09-6c2a-4011-8613-778778f3e4ac" />

O filtro tcp.flags.syn == 1 permite monitorar exclusivamente as tentativas de abertura de conexão vindas do host infectado (192.168.56.10) A análise detalhada foca no comportamento do protocolo de transporte.

O script em PowerShell ignora o fato de o servidor C2 estar derrubando as conexões na porta 80. Ele continua forçando as novas solicitações de sincronização [SYN] continuamente para tentar estabelecer o canal.

O wireshark destaca em vermelho o reuso de portas efêmeras ([TCP Port numbers reused]) como o loop de execução tenta abrir nova conexões de forma extremamente rápida e agressiva , o sistema operacional esgota as portas de saída disponíveis e é obrigado a reciclar numerações de portas usadas segundos antes (como a porta 50049)

## Detalhes fo pacote 

<img width="566" height="222" alt="detalhes do pacote" src="https://github.com/user-attachments/assets/195b6e56-347b-47aa-9bdd-3a164ddfd9dc" />

Ao inspecionar os detalhes interno da camada de transporte (TCP) no pacote enviado pela máquina Windons , conseguimos extrair evidências estruturais cruciais sobre a anomalia na rede:

Conversations completeness: o próprio wireshark analisa o comportamento do fluxo e gera esse metadado automática, confirmando de forma analítica que o aperto de mão (handshake) de 3 vias falhou e a conversa nunca chegou a ser completada.

TCP Segment Len: 0 O comprimento do segmento zerado comprova que o script em PowerShell não conseguiu enviar nenhuma informação útil da camada de aplicação (nenhum dado de requisição HTTP chegou a trafegar), pois a sessão TCP foi abortada logo no primeiro passo. 

## TCP Flags 
<img width="507" height="292" alt="detalhes flags" src="https://github.com/user-attachments/assets/7a217ff4-516f-4466-8c3a-176021089a6f" />

Para auditar a anatomia do tráfego ruidoso gerado pelo host windons expandi o campo de Flags no cabeçalho do pacote TCP. A análise microscópica revela o bit Syn: Set(1) ativado, mapeado pelo valor hexadecial 0X002 

A ativação exclusiva desse bit comprova que o pacote é estritamente uma solicitação de sincronização inicial . O fato de todas as outras flags de controle como acknowledgment ,Push ou Fin estarem desativadas (not set) materializa o comportamento isistente e repetitivo do artefeto malicioso: ele gera apenas o primeiro passo do handshake, tentando sem para forçar uma abertuda de canal com a infraestrutura de comando e controle (c2). 

## Hex Dump
<img width="690" height="108" alt="flag reset hexa" src="https://github.com/user-attachments/assets/639d61ad-fbb5-4e19-9e85-a8259d5fd12c" />

Para consolidar a investigação analisei o Hex Dump do pacote para mapear como as instruções de rede são estruturadas diretamenta na memória camda física/enlace 

Como exemplo de correlação ao inspecionarmos o deslocamento dos bytes na linha 0020, conseguimos identificar o byte 02, que corresponde exatamento ao valor hexadecimal 0x002 da flag SYN que isolamos na análise de detalhes

## IOCs
- Requisições SYN repetitivas
- Reuso de portas TCP
- Comunicação periódica
- Tentativas contínuas de handshake
- Host 192.168.56.10
- Destino 192.168.56.20
- Beacon interval de 10 segundos

## Miticações
- Alertas para excesso de conexões SYN
- Restrição de scripts PowerShell não autorizados
- Monitoramento contínuo de tráfego TCP suspeito
- Bloqueio de IPs/domínios suspeitos

