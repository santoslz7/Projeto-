#include <stdio.h>

const int IN1 = 26;   
const int IN2 = 28;   
const int IN3 = 32;  
const int IN4 = 34;   

const int TrigPinE = 2;
const int TrigPinD = 11;
const int TrigPinC = 4;
const int EchoPinE = 3;
const int EchoPinD = 5;
const int EchoPinC = 12;

const int sensorLinhaEsquerdo = 6;
const int sensorLinhaDireito = 8;
const int sensorLinhaCentro = 7;

long duracao, distancia;

const int LIMIAR_LINHA = 500; 

void setup() {
 
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);

  
  pinMode(TrigPinE, OUTPUT);
  pinMode(TrigPinD, OUTPUT);
  pinMode(TrigPinC, OUTPUT);
  pinMode(EchoPinE, INPUT);
  pinMode(EchoPinD, INPUT);
  pinMode(EchoPinC, INPUT);

  Serial.begin(9600); 
  delay(2000); 
}

void loop() {
 
  int leituraEsq = analogRead(sensorLinhaEsquerdo);
  int leituraDir = analogRead(sensorLinhaDireito);
  int leituraCent = analogRead(sensorLinhaCentro);
  
  if (leituraEsq > LIMIAR_LINHA || leituraDir > LIMIAR_LINHA || leituraCent > LIMIAR_LINHA) {
    
    recuarETornar();
  }
  else {
   
    distancia = lerDistanciaUltrassonico();

    if (distancia < 20) {
      atacarFrente();
    }
    else {
      
      procurarOponente();
    }
  }
}

void parar() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, LOW);
}

void atacarFrente() {
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
 
}

void recuarETornar() {
 
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
  delay(300); // Tempo de recuo
  
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
  delay(400); // Tempo de giro

  parar();
}

void procurarOponente() {
 
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
}

long lerDistanciaUltrassonico() {
  
  digitalWrite(TrigPinD, LOW);
  delayMicroseconds(2);
 
  digitalWrite(TrigPinD, HIGH);
  delayMicroseconds(10);
  digitalWrite(TrigPinD, LOW);
  
  duracao = pulseIn(EchoPinD, HIGH);

  distancia = duracao * 0.0343 / 2;
  return distancia;
}
