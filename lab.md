# Ambiente do Lab

<img width="488" height="287" alt="adaptador1" src="https://github.com/user-attachments/assets/e45ceda5-a5e6-4abd-8351-aac427f6e990" />
<img width="482" height="274" alt="adaptador 2" src="https://github.com/user-attachments/assets/f72c8a37-3632-4b04-bbe1-47fad627c9b1" />

Para a execução de testes com payload baseado em Invoke-WebRequest , a máquina foi estruturada com duas placas de rede. 
A primeira interface atua em modo NAT para manter serviços essenciais, enquanto a segunda foi alocada em uma rede interna (itnet).Essa separação é fundamental pois permite analise de tráfego sem ruído local na rede.

<img width="841" height="181" alt="servidor porta 8080  recorte" src="https://github.com/user-attachments/assets/50e83f55-0e70-4351-b218-3c2e0d37c5aa" />

Foi utilizado o kali linux para simular a máquina do atacante. É necessário identificar o endereço ip da rede (192.168.56.20) e em seguida iniciar o servidor http simples usando python na porta 8080. Este servidor atuará como o ponto central para receber conexões (beacons) vindas da máquina Windons infectada.

<img width="612" height="164" alt="ip windons" src="https://github.com/user-attachments/assets/db1bcd0e-f6eb-4865-a001-e83a28d9650a" />

Identificado e configurado o IPv4 do Windons do adaptador 2 de rede

# Simulação C2

<img width="316" height="69" alt="geramento de trafego c2" src="https://github.com/user-attachments/assets/fe0004a8-46d9-4a78-a556-1f119a1d70f7" />

Na máquina infectada, foi utilizado um script em PowerShell para simular o comportamento do malware. O comando Invoke - WebRequest realiza a chamada ao servidor C2, enquanto o Start - Sleep - Seconds 10 define o intervalo de check - in . Este loop infinito garante que a persistência da comunicação seja mantida, enviando um sinal ao atacante a cada 10 segundos.


<img width="743" height="128" alt="image" src="https://github.com/user-attachments/assets/55be560b-9cd9-441a-b5b3-2e4ac06268da" />

Utilizando o filtro ip.dst == 192.168.56.20 , faz a filtragem dos pacotes recebidos pela máquina Windons (origem 192.168.56.10) que está tentando iniciar coneões ([SYN]) sem parar.
O grande volume de alertas em vermelho com o aviso [tcp port numbers reused] é a prova visual do ataque , o  script em PowerShell gerou requisições tão rápidas e frequentes que o sistema operacional esgotou as portas de redes disponíveis sendo obrigado a reutilizá-las.
<img width="1112" height="116" alt="ip  recorte" src="https://github.com/user-attachments/assets/cc3d44a4-8197-4d2c-8c75-3f600ebad99d" />




