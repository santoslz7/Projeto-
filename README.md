// Definição dos pinos para a Ponte H (L298N)
const int IN1 = 26;   // Motor esquerdo frente
const int IN2 = 28;   // Motor esquerdo trás
const int IN3 = 32;   // Motor direito frente
const int IN4 = 34;   // Motor direito trás

// Definição dos pinos para o Sensor Ultrassônico (HC-SR04)
const int TrigPinE = 2;
const int TrigPinD = 11;
const int TrigPinC = 4;
const int EchoPinE = 3;
const int EchoPinD = 5;
const int EchoPinC = 12;

// Definição dos pinos para os Sensores de Linha (analógicos)
const int sensorLinhaEsquerdo = 6;
const int sensorLinhaDireito = 8;
const int sensorLinhaCentro = 7;

// Variáveis para o sensor ultrassônico
long duracao, distancia;

const int LIMIAR_LINHA = 500; // Ajuste este valor

void setup() {
  // Configuração dos pinos dos motores como saída
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);

  // Configuração dos pinos do sensor ultrassônico
  pinMode(TrigPinE, OUTPUT);
  pinMode(TrigPinD, OUTPUT);
  pinMode(TrigPinC, OUTPUT);
  pinMode(EchoPinE, INPUT);
  pinMode(EchoPinD, INPUT);
  pinMode(EchoPinC, INPUT);

  Serial.begin(9600); // Inicia comunicação serial para debug, se necessário
  delay(2000); 

void loop() {
  //Leitura dos sensores de linha
  int leituraEsq = analogRead(sensorLinhaEsquerdo);
  int leituraDir = analogRead(sensorLinhaDireito);
  int leituraCent = analogRead(sensorLinhaCentro);

  // Verificação de bordas
  if (leituraEsq > LIMIAR_LINHA || leituraDir > LIMIAR_LINHA || leituraCent > LIMIAR_LINHA) {
    // Detectou a linha branca (ou preta, dependendo da sua arena/sensor)
    recuarETornar();
  }
  else {
  
   distancia = lerDistanciaUltrassonico();
    if (distancia < 20) { // Oponente detectado a menos de 20 cm
      atacarFrente();
    }
    else {
      // 4. Se não houver obstáculo próximo, procurar pelo oponente (girar)
      procurarOponente();
    }
  }
}

//Funções de Movimento 

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
  // Recuar um pouco
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
  delay(300);

  //Girar para longe da linha
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
  delay(400); // Tempo de giro

  parar();
}

void procurarOponente() {
  // Girar no próprio eixo para procurar o oponente
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
}

//Função do Sensor Ultrassônico 

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
