# 🚦 Projeto: Semáforo Orientado a Objetos com Arduino

## 📘 Descrição Geral

Este projeto implementa um **sistema de semáforo em C++ utilizando Programação Orientada a Objetos (POO)** no Arduino.  
O semáforo é controlado por um **botão**, que simula a solicitação de travessia de pedestres. O código gerencia os LEDs de forma sequencial, aplicando conceitos de encapsulamento, construtores e métodos de classe.

O sistema utiliza **três LEDs verdes** para representar diferentes estados de tráfego, além de LEDs vermelho e amarelo para o controle de fluxo.

---

## 🧠 Objetivos do Projeto

- Aplicar **conceitos de Programação Orientada a Objetos (POO)** em um contexto prático com Arduino.  
- Demonstrar o uso de **métodos e atributos privados e públicos**.  
- Simular o **funcionamento real de um semáforo**, com transições automáticas controladas por botão.  
- Promover o entendimento da **estrutura modular e reutilizável do código**.

---

## ⚙️ Componentes Utilizados

| Componente          | Quantidade | Função                                   |
|---------------------|-------------|------------------------------------------|
| Arduino UNO         | 1           | Microcontrolador principal               |
| LED vermelho        | 1           | Indica parada de veículos                |
| LED amarelo         | 1           | Indica atenção (transição)               |
| LEDs verdes         | 3           | Indicam passagem em diferentes estados   |
| Botão (push button) | 1           | Acionamento do ciclo de semáforo         |
| Resistores (220 Ω)  | 5           | Proteção dos LEDs                        |
| Jumpers e protoboard| -           | Conexões elétricas                       |

---

## 🔌 Esquema de Ligações

| Pino Arduino | Componente          | Descrição               |
|---------------|--------------------|--------------------------|
| 2             | LED vermelho        | Parada                   |
| 3             | LED amarelo         | Atenção                  |
| 4             | LED verde 1         | Passagem (nível 1)       |
| 5             | LED verde 2         | Passagem (nível 2)       |
| 6             | LED verde 3         | Passagem (nível 3)       |
| 7             | Botão               | Solicitação de travessia |
| GND           | Comum               | Terra dos LEDs e botão   |

> 💡 **Dica:** Utilize resistores de 220 Ω para cada LED para evitar sobrecarga.

---

## 🧩 Estrutura do Código

O projeto é composto por uma **classe principal** chamada `TrafficLight`, responsável por todo o controle do semáforo, e um **código principal** que inicializa e executa o sistema.

### Classe `TrafficLight`

| Tipo | Nome | Função |
|------|------|--------|
| `int` | `buttonPin` | Pino do botão |
| `int` | `redLed`, `yellowLed`, `greenLed1`, `greenLed2`, `greenLed3` | Pinos dos LEDs |
| `int` | `buttonState`, `counter` | Controle interno do ciclo |

#### 🧱 Métodos principais

- **`TrafficLight(int button, int red, int yellow, int g1, int g2, int g3)`**  
  Construtor que define os pinos do semáforo.

- **`void setup()`**  
  Configura todos os pinos como entrada ou saída.

- **`void readButton()`**  
  Lê o estado atual do botão de acionamento.

- **`void setLight(int led, bool state)`**  
  Liga ou desliga o LED especificado.

- **`void run()`**  
  Executa o comportamento completo do semáforo de acordo com o estado do botão.

---

## 🔁 Lógica de Funcionamento

1. **Sem botão pressionado (`LOW`)**  
   - LED vermelho **ligado**  
   - LED verde 3 **ligado** (estado inicial de tráfego)

2. **Com botão pressionado (`HIGH`)**  
   - O LED amarelo acende por 1 segundo.  
   - O LED amarelo **pisca 3 vezes** como aviso.  
   - LEDs verdes 1 e 2 acendem por 5 segundos, simulando o tráfego fluindo.  
   - Após esse tempo, apenas o LED verde 3 fica aceso, retornando ao estado inicial.

---

Vídeo:
https://drive.google.com/drive/u/1/folders/1zckFXhM1T5n-AGcHhKmX7YNPgIxB3Q1s

## 💡 Código Completo

```cpp
// C++ code orientado a objetos

class TrafficLight {
  private:
    int buttonPin;
    int redLed;
    int yellowLed;
    int greenLed1;
    int greenLed2;
    int greenLed3;
    int buttonState;
    int counter;

  public:
    // Construtor
    TrafficLight(int button, int red, int yellow, int g1, int g2, int g3) {
      buttonPin = button;
      redLed = red;
      yellowLed = yellow;
      greenLed1 = g1;
      greenLed2 = g2;
      greenLed3 = g3;
    }

    // Inicializa os pinos
    void setup() {
      pinMode(buttonPin, INPUT);
      pinMode(redLed, OUTPUT);
      pinMode(yellowLed, OUTPUT);
      pinMode(greenLed1, OUTPUT);
      pinMode(greenLed2, OUTPUT);
      pinMode(greenLed3, OUTPUT);
    }

    // Lê o estado do botão
    void readButton() {
      buttonState = digitalRead(buttonPin);
    }

    // Acende e apaga LEDs
    void setLight(int led, bool state) {
      digitalWrite(led, state ? HIGH : LOW);
    }

    // Executa o comportamento do semáforo
    void run() {
      readButton();

      if (buttonState == HIGH) {
        setLight(redLed, LOW);
        setLight(yellowLed, HIGH);
        delay(1000);

        for (counter = 0; counter < 3; ++counter) {
          setLight(yellowLed, LOW);
          delay(1000);
          setLight(yellowLed, HIGH);
          delay(1000);
        }

        setLight(yellowLed, LOW);
        setLight(greenLed1, HIGH);
        setLight(greenLed2, HIGH);
        setLight(greenLed3, LOW);
        delay(5000);

        setLight(yellowLed, LOW);
        setLight(greenLed1, LOW);
        setLight(greenLed2, LOW);
        setLight(greenLed3, HIGH);
      } 
      else {
        setLight(redLed, HIGH);
        setLight(greenLed3, HIGH);
      }
    }
};

// =========================
// Código principal do Arduino
// =========================

TrafficLight semaforo(7, 2, 3, 4, 5, 6);

void setup() {
  semaforo.setup();
}

void loop() {
  semaforo.run();
}
