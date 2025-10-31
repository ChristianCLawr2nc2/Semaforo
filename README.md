# 🚦 Projeto Semáforo Inteligente com LED RGB para Pedestre (Arduino + POO em C++)

## 📘 Descrição Geral

Este projeto implementa um **sistema de semáforo inteligente** utilizando **Arduino** e o conceito de **Programação Orientada a Objetos (POO)** em **C++**.  
O sistema controla dois conjuntos de sinais:

- Um **semáforo de veículos**, com LEDs **vermelho** e **amarelo**.
- Um **semáforo de pedestres**, representado por um **LED RGB** que alterna entre **vermelho** (não atravessar) e **verde** (atravessar).

O objetivo é demonstrar a aplicação de conceitos de **encapsulamento, abstração e interação entre classes** no controle de hardware físico.

---

## 🧩 Funcionalidades

- Controle do fluxo entre veículos e pedestres.  
- LED RGB utilizado para indicar o status do pedestre:
  - 🔴 **Vermelho** → Aguardar.  
  - 🟢 **Verde** → Atravessar.  
- Botão físico que **simula o pedido de travessia do pedestre**.  
- Temporizações realistas com **delays** e **piscar do amarelo** antes da transição.  
- Arquitetura baseada em **duas classes**: `TrafficLight` e `PedestrianLight`.

---

## ⚙️ Componentes Utilizados

| Quantidade | Componente                | Função                                |
|-------------|---------------------------|----------------------------------------|
| 1           | Arduino Uno R3            | Unidade de controle principal          |
| 1           | LED Vermelho              | Sinal vermelho do semáforo de veículos |
| 1           | LED Amarelo               | Sinal amarelo do semáforo de veículos  |
| 1           | LED RGB (comum cátodo)    | Sinal de pedestre (vermelho e verde)   |
| 1           | Botão (push button)       | Simula o botão de travessia            |
| 4           | Resistores de 220Ω        | Proteção dos LEDs                      |
| 1           | Protoboard + jumpers      | Conexões do circuito                   |

---

## 🔌 Ligações no Arduino

| Componente             | Pino Arduino | Observação |
|------------------------|---------------|-------------|
| LED Vermelho (veículo) | 2             | Saída digital |
| LED Amarelo (veículo)  | 3             | Saída digital |
| LED RGB - Vermelho     | 5 (PWM)       | Saída PWM |
| LED RGB - Verde        | 6 (PWM)       | Saída PWM |
| LED RGB - Azul         | 9 (PWM)       | Saída PWM (não usado) |
| Botão                  | 7             | Entrada digital (INPUT) |
| GND comum do circuito  | GND           | Comum para todos os LEDs e botão |

> ⚠️ **Importante:** Utilize resistores em série com cada terminal de LED para evitar danos ao Arduino.

---

## 🧠 Estrutura Orientada a Objetos

O código é organizado em duas classes principais:

### 🟢 Classe `PedestrianLight`

Responsável pelo **controle do LED RGB do pedestre**.

**Principais métodos:**
- `setup()` → configura os pinos como saída.  
- `setColor(r, g, b)` → define a cor do LED RGB.  
- `red()` → acende o vermelho.  
- `green()` → acende o verde.  
- `off()` → desliga o LED.

---

### 🔴 Classe `TrafficLight`

Responsável pelo **controle do semáforo de veículos** e pela **lógica de interação** com o pedestre.

**Principais métodos:**
- `setup()` → inicializa todos os pinos.  
- `run()` → executa o ciclo principal do semáforo:
  1. Espera o botão ser pressionado.  
  2. Ativa o amarelo.  
  3. Dá sinal verde para o pedestre e vermelho para os carros.  
  4. Retorna ao estado inicial.

---

## 🧾 Código-Fonte

```cpp

// Classe que representa o LED RGB do pedestre
class PedestrianLight {
  private:
    int redPin;
    int greenPin;
    int bluePin;

  public:
    // Construtor
    PedestrianLight(int r, int g, int b) {
      redPin = r;
      greenPin = g;
      bluePin = b;
    }

    // Inicializa os pinos
    void setup() {
      pinMode(redPin, OUTPUT);
      pinMode(greenPin, OUTPUT);
      pinMode(bluePin, OUTPUT);
      off(); // começa desligado
    }

    // Define a cor do LED RGB
    void setColor(int redValue, int greenValue, int blueValue) {
      analogWrite(redPin, redValue);
      analogWrite(greenPin, greenValue);
      analogWrite(bluePin, blueValue);
    }

    // Vermelho (pedestre deve esperar)
    void red() {
      setColor(255, 0, 0);
    }

    // Verde (pedestre pode atravessar)
    void green() {
      setColor(0, 255, 0);
    }

    // Desliga o LED
    void off() {
      setColor(0, 0, 0);
    }
};

// Classe principal do semáforo de veículos
class TrafficLight {
  private:
    int redLed;
    int yellowLed;
    PedestrianLight &pedestrian;

  public:
    // Construtor
    TrafficLight(int red, int yellow, PedestrianLight &ped)
      : pedestrian(ped) {
      redLed = red;
      yellowLed = yellow;
    }

    // Inicializa os pinos
    void setup() {
      pinMode(redLed, OUTPUT);
      pinMode(yellowLed, OUTPUT);
      pedestrian.setup();
    }

    // Executa o ciclo automático do semáforo
    void run() {
      // Estado 1: Verde para carros, vermelho para pedestres
      digitalWrite(redLed, LOW);
      digitalWrite(yellowLed, LOW);
      pedestrian.red();
      delay(5000); // carros passando

      // Estado 2: Amarelo para carros
      digitalWrite(yellowLed, HIGH);
      delay(2000);
      digitalWrite(yellowLed, LOW);

      // Estado 3: Vermelho para carros, verde para pedestres
      digitalWrite(redLed, HIGH);
      pedestrian.green();
      delay(5000); // pedestres atravessando

      // Estado 4: Pisca verde do pedestre antes de fechar
      for (int i = 0; i < 3; i++) {
        pedestrian.off();
        delay(400);
        pedestrian.green();
        delay(400);
      }

      // Retorna ao início do ciclo
      digitalWrite(redLed, LOW);
      pedestrian.red();
    }
};

// =========================
// Código principal do Arduino
// =========================

// Pinos do LED RGB (pedestre) - use pinos PWM (~)
PedestrianLight pedestre(5, 6, 9);

// Pinos: vermelho e amarelo do semáforo de veículos
TrafficLight semaforo(2, 3, pedestre);

void setup() {
  semaforo.setup();
}

void loop() {
  semaforo.run();
}

