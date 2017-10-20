#include <VirtualWire.h> //Inclui a biblioteca VirtualWire.h
 char *letra = "S"; //Cria a variável letra com o valor "S"
 
//Constante para os pinos dos botões
int xPin = A1;
int yPin = A2;
 
 
//variáveis de Controle de cada botão
int xEixo = 0;
int yEixo = 0;

void setup()
{
  //++++++++++++++Inicializa o módulo transmissor+++++++++++++++++++
  vw_set_ptt_inverted(true); //Configuração da Biblioteca
  vw_setup(2000); //Configuração da Biblioteca
  vw_set_tx_pin(3); //Configura o pino D3 para a leitura dos dados
  //================================================================
   Serial.begin(9600); 
  pinMode(xPin, INPUT);
  pinMode(yPin, INPUT);
  //======================================
}

void loop()
{
//Lê o Status dos botoes
  xEixo = analogRead(xPin);
  xEixo = map(xEixo, 0, 1023, 1, 180);
  yEixo = analogRead(yPin);
  yEixo = map(yEixo, 0, 1023, 1, 180);

  //Monitoramento
  Serial.print("X: ");
  Serial.print(xEixo);
  Serial.print(" | Y: ");
  Serial.print(yEixo);
  Serial.print(" | Emitindo: ");
  Serial.println(letra);
  
  //Verifica se tem algum botão pressionado para enviar a seu respectivo comando (letra)
  if( xEixo > 100 && yEixo > 75 && yEixo < 100 ) letra = "F"; //Envia "F" - FORWARD
  else if( xEixo < 21 && yEixo > 85 && yEixo < 100 ) letra = "B"; //Envia "B" - BACKWARD
  else if( xEixo > 0 && yEixo > 100 ) letra = "R"; //Envia "R" - RIGHT+FORWARD
  else if( xEixo > 0 && yEixo > 0 &&yEixo < 63 ) letra = "L"; //Envia "L" - LEFT+FORWARD
  else letra = "S";

 //Transmiste a variável letra 3 vezes
  for(int i=0 ; i<3 ; i++)
  {
  vw_send((uint8_t *)letra, strlen(letra)); //Envia a variável letra
  vw_wait_tx(); //Aguarda o fim de transmissão
  delay(10); //Aguarda 10 milissegundos
  }
}
