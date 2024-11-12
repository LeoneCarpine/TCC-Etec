# TCC-Etec
TCC Etec - Dispenser de álcool em gel automático
Desenvolvido durante a pandemia, o dipsenser de álcool em gel tem por objetivo higienizar a mão do usuário sem que ele entre em contato com o recipiente de álcool. Ao  aproximar as mãos o servo motor aciona que derrama uma quantidade suficiente de álcool em gel para que o usuário possa se higienizar.

==========================================================================================================================================================================================
#include <Servo.h> //inclui biblioteca do servo

Servo servo1;  //inicializa o servo
int pos = 0;   
int servinho = 9; //declara o pino do sinal do servo

int vermelho = 5; //declara pino da cor vermelha
int verde = 6;    //declara pino da cor verde
int azul = 7;     //declara pino da cor azul

int pinoReceptor = 3; // echo     --- amarelo
int pinoTransmissor = 4; // trig  --- azul


//-----------------SETUP-------------------------
void setup() {

  pinMode(azul, OUTPUT);     //tipo de pinagem cor azul do led rgb
  pinMode(verde, OUTPUT);    //tipo de pinagem cor verde do led rgb
  pinMode(vermelho, OUTPUT); //tipo de pinagem cor vermelha do led rgb

  pinMode(pinoReceptor, INPUT);     //tipo de pinagem echo do sensor ultrassônico
  pinMode(pinoTransmissor, OUTPUT); //tipo de pinagem trig do sensor ultrassônico

  servo1.attach(servinho); //tipo de pinagem servo/inicializa o servo
  servo1.write(0);         //posição inicial do servo 0
  Serial.begin(9600);      //inicia monitor serial para monitoramento com serial print
}

//-----------------LOOP-------------------------
void loop() {

  Serial.println(lerDistancia()); //printa distancia para monitoramento
  
  if (lerDistancia() <= 15) {  //le sê distancia é menor ou igual a 15cm

    controlarCores (100, 255, 0);         //acende cor do led
    Serial.println("* Liberando alcool"); //printa "*Liberando alcool" para monitoramento

    servo1.write(-80); //faz servo girar 80 graus
    delay(1000);       //espera 1 segundo
    servo1.write(90);  //faz servo voltar 90 graus
    delay(3000);       //espera 3 segundos
  }

  if (lerDistancia() > 15) {        //le sê distancia é maior que 15
    Serial.println("@ Nao tem ngm");//printa "@ Nao tem ngm" para monitoramento
    controlarCores (0, 0, 0);       //apaga cores
    servo1.write(0);                //coloca servo na posição 0
  }

}

//------FUNÇÕES---------------

//nome da função a ser chamada retornando o tipo float
float lerDistancia() {  
  float duracao; //cria uma variável do tipo float para fazer leitura do sensor
  
  digitalWrite(pinoTransmissor, 1); //liga o pino transmissor
  delayMicroseconds(10);            //espera de 10 micro segundos
  digitalWrite(pinoTransmissor, 0); //desliga pino transmissor

  duracao = pulseIn(pinoReceptor, 1); //pega valor que o pino receptor le e coloca em uma var nova

  //multiplica a var por 0.017 para transformar a leitura feira em cm e retorna para a função
  return duracao * 0.017;
}
  
//função para controlar cores do led
void controlarCores (int nomeCor, int nomeCor2, int nomeCor3) { 
  analogWrite (vermelho, nomeCor); //declara cor do led a ser usado e qual o valor
  analogWrite (azul, nomeCor2);    //declara cor do led a ser usado e qual o valor
  analogWrite (verde, nomeCor3);   //declara cor do led a ser usado e qual o valor
}
