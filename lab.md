# Ambiente do Lab

<img width="488" height="287" alt="adaptador1" src="https://github.com/user-attachments/assets/e45ceda5-a5e6-4abd-8351-aac427f6e990" />
<img width="482" height="274" alt="adaptador 2" src="https://github.com/user-attachments/assets/f72c8a37-3632-4b04-bbe1-47fad627c9b1" />

Para a execução de testes com payload baseado em Invoke-WebRequest , a máquina foi estruturada com duas placas de rede. 
A primeira interface atua em modo NAT para manter serviços essenciais, enquanto a segunda foi alocada em uma rede interna (itnet).Essa separação é fundamental pois permite analise de tráfego sem ruído local na rede.

<img width="841" height="181" alt="servidor porta 8080  recorte" src="https://github.com/user-attachments/assets/50e83f55-0e70-4351-b218-3c2e0d37c5aa" />

Foi utilizado o kali linux para simular a máquina do atacante. É necessário identificar o endereço ip da rede (192.168.56.20) e em seguida iniciar o servidor http simples usando python na porta 8080. Este servidor atuará como o ponto central para receber conexões (beacons) vindas da máquina Windons infectada.

<img width="612" height="164" alt="ip windons" src="https://github.com/user-attachments/assets/db1bcd0e-f6eb-4865-a001-e83a28d9650a" />


