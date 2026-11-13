# Arduino5
Задания группы д54.07 для 5 урока. Создание, симулирование и сборка 
ри включении платы. Здесь мы объясняем Arduino, к каким дырочкам подключены лампочки (`OUTPUT` значит «отдавать ток»).
* **`void loop()`** — бесконечный цикл. Команды внутри выполняются по очереди сверху вниз, а потом всё начинается сначала.
* **`digitalWrite(пин, HIGH)`** — подать ток на пин (включить лампочку). **`LOW`** — убрать ток (выключить).
* **`delay(3000)`** — поставить программу на паузу. Время пишется в миллисекундах (`1000 мс = 1 секунда`).

---

### Задание 4. Умный пешеходный переход (с кодом)

#### Что нужно сделать
Оставьте светофор для машин из прошлого задания и добавьте к нему:
1. **Светофор для пешеходов** (через резисторы на `GND`):
   * Красный $\rightarrow$ пин `10`
   * Зеленый $\rightarrow$ пин `9`
2. **Тактовую кнопку** (по центру платы через канавку):
   * Одна ножка $\rightarrow$ пин `2`
   * Ножка по диагонали $\rightarrow$ общий минус `GND`

**Что должно получиться:** Пока кнопку никто не нажимает, машинам горит зеленый, а пешеходам — красный. Как только пешеход нажал кнопку, через 2 секунды машинам включается желтый, потом красный, а пешеходам загорается зеленый. Через 5 секунд пешеходный зеленый мигает 3 раза (предупреждает, что пора заканчивать переход), и всё возвращается обратно.

#### Готовый код
```cpp
// === НАСТРОЙКИ ===
int carRed = 13;
int carYellow = 12;
int carGreen = 11;

int pedRed = 10;
int pedGreen = 9;

int buttonPin = 2;

int waitBeforeChange = 2000;
int yellowTime = 1500;
int walkTime = 5000;

// === ОСНОВНАЯ ПРОГРАММА ===
void setup() {
  pinMode(carRed, OUTPUT);
  pinMode(carYellow, OUTPUT);
  pinMode(carGreen, OUTPUT);
  pinMode(pedRed, OUTPUT);
  pinMode(pedGreen, OUTPUT);

  pinMode(buttonPin, INPUT_PULLUP);

  // Исходное состояние при включении
  digitalWrite(carGreen, HIGH);
  digitalWrite(pedRed, HIGH);
}

void loop() {
  int buttonState = digitalRead(buttonPin);

  if (buttonState == LOW) {
    delay(waitBeforeChange);

    // Машинам желтый
    digitalWrite(carGreen, LOW);
    digitalWrite(carYellow, HIGH);
    delay(yellowTime);

    // Машинам красный
    digitalWrite(carYellow, LOW);
    digitalWrite(carRed, HIGH);
    delay(500);

    // Пешеходам зеленый
    digitalWrite(pedRed, LOW);
    digitalWrite(pedGreen, HIGH);
    delay(walkTime);

    // Мигание пешеходного зеленого 3 раза
    for (int i = 0; i < 3; i++) {
      digitalWrite(pedGreen, LOW);
      delay(300);
      digitalWrite(pedGreen, HIGH);
      delay(300);
    }

    // Возврат в исходное состояние
    digitalWrite(pedGreen, LOW);
    digitalWrite(pedRed, HIGH);
    delay(500);

    digitalWrite(carYellow, HIGH);
    delay(yellowTime);
    digitalWrite(carRed, LOW);
    digitalWrite(carYellow, LOW);
    digitalWrite(carGreen, HIGH);
  }
}
```

#### 🔍 Как работает этот код
* **`INPUT_PULLUP`** — включаем встроенный внутрь Arduino резистор для кнопки. Благодаря ему нам не нужно ставить лишний резистор на макетную плату. Пока кнопка не нажата, на пине `2` есть сигнал (`HIGH`). Когда нажимаем — сигнал пропадает (`LOW`).
* **`if (buttonState == LOW)`** — проверка условия («если кнопка нажата»). Команды внутри фигурных скобок `{ ... }` сработают только в тот момент, когда кто-то нажмет кнопку.
* **`for (int i = 0; i < 3; i++)`** — счетчик повторений. Чтобы не писать одно и то же выключение и включение лампочки три раза подряд, этот цикл сам повторяет команды внутри скобок ровно 3 раза.
