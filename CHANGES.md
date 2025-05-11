# Зміни в проекті Tic Tac Toe

## 1. Додавання темної теми
### Конкретні зміни:
```kotlin
// Додано стан для темної теми
var isDarkTheme by remember { mutableStateOf(false) }

// Обгортання всього додатку в тему
LessonTicTacToeTheme(darkTheme = isDarkTheme) {
    // ... код додатку ...
}

// Перемикач теми
Surface(
    modifier = Modifier
        .align(Alignment.CenterEnd)
        .padding(end = 8.dp)
        .size(40.dp)  // Фіксований розмір для кращої доступності
        .clip(CircleShape)
        .clickable { isDarkTheme = !isDarkTheme }
        .shadow(4.dp, CircleShape),  // Тінь для 3D ефекту
    shape = CircleShape,
    color = MaterialTheme.colorScheme.primaryContainer  // Використання системного кольору
) {
    Text(
        text = if (isDarkTheme) "☀️" else "🌙",
        style = MaterialTheme.typography.titleMedium  // Оптимальний розмір для емоджі
    )
}
```
### Вплив на UX:
- Перемикач теми розміщений у верхньому правому куті для легкого доступу
- Розмір 40.dp забезпечує достатню область для натискання
- Тінь 4.dp створює візуальну глибину
- Емоджі змінюються для інтуїтивного розуміння поточної теми

## 2. Покращення ігрового поля
### Конкретні зміни:
```kotlin
// Клітинка ігрового поля
Surface(
    modifier = Modifier
        .size(100.dp)  // Оптимальний розмір для зручного натискання
        .padding(4.dp)  // Відступ між клітинками
        .shadow(4.dp, RoundedCornerShape(8.dp))  // Тінь для 3D ефекту
        .clickable { /* обробка ходу */ }
        .scale(scale),  // Анімація масштабування
    shape = RoundedCornerShape(8.dp),  // Заокруглення кутів
    color = MaterialTheme.colorScheme.surface,  // Фоновий колір
    border = BorderStroke(2.dp, MaterialTheme.colorScheme.primary)  // Рамка
) {
    Box(
        modifier = Modifier.fillMaxSize(),
        contentAlignment = Alignment.Center
    ) {
        Text(
            text = field[index],
            style = MaterialTheme.typography.displayLarge.copy(
                fontSize = 48.sp,  // Оптимальний розмір для X та O
                fontWeight = FontWeight.Bold  // Покращена видимість
            ),
            color = when (field[index]) {
                "X" -> MaterialTheme.colorScheme.primary  // Колір для X
                "O" -> MaterialTheme.colorScheme.secondary  // Колір для O
                else -> MaterialTheme.colorScheme.onSurface.copy(alpha = 0.3f)  // Прозорість для порожніх клітинок
            }
        )
    }
}
```
### Вплив на UX:
- Розмір клітинки 100.dp забезпечує зручне натискання
- Відступ 4.dp між клітинками покращує візуальне розділення
- Рамка 2.dp робить клітинки більш помітними
- Розмір шрифту 48.sp забезпечує хорошу читабельність
- Прозорість 0.3f для порожніх клітинок створює візуальну ієрархію

## 3. Покращення таймера
### Конкретні зміни:
```kotlin
// Анімація появи/зникнення таймера
AnimatedVisibility(
    visible = gameStatus.isEmpty() && !isFirstMove,  // Показуємо тільки після першого ходу
    enter = fadeIn() + expandVertically(),  // Плавна поява
    exit = fadeOut() + shrinkVertically()  // Плавне зникнення
) {
    Column(
        modifier = Modifier
            .padding(bottom = 16.dp)
            .shadow(4.dp, RoundedCornerShape(16.dp))
            .clip(RoundedCornerShape(16.dp))
            .background(MaterialTheme.colorScheme.surface)
            .padding(16.dp)
    ) {
        Text(
            text = "Time left: $timeLeft s",
            color = if (timeLeft <= 3) Color.Red else MaterialTheme.colorScheme.onBackground  // Червоний колір при закінченні часу
        )
        LinearProgressIndicator(
            progress = timeLeft / 10f,  // Прогрес від 0 до 1
            modifier = Modifier
                .fillMaxWidth()
                .padding(horizontal = 32.dp)
                .height(8.dp),  // Висота індикатора
            color = if (timeLeft <= 3) Color.Red else MaterialTheme.colorScheme.primary
        )
    }
}
```
### Вплив на UX:
- Таймер з'являється тільки після першого ходу для зменшення відволікання
- Червоний колір при timeLeft <= 3 створює відчуття терміновості
- Висота індикатора 8.dp забезпечує хорошу видимість
- Відступи 16.dp та 32.dp створюють збалансований дизайн

## 4. Покращення кнопок керування
### Конкретні зміни:
```kotlin
// Кнопки з анімацією та тактильним відгуком
Button(
    onClick = { 
        haptic.performHapticFeedback(HapticFeedbackType.TextHandleMove)  // Тактильний відгук
        resetRound() 
    },
    modifier = Modifier
        .weight(1f)  // Рівний розподіл простору
        .height(48.dp)  // Стандартна висота кнопки
        .shadow(4.dp, RoundedCornerShape(8.dp)),  // Тінь для 3D ефекту
    colors = ButtonDefaults.buttonColors(
        containerColor = MaterialTheme.colorScheme.primaryContainer,  // Фоновий колір
        contentColor = MaterialTheme.colorScheme.onPrimaryContainer  // Колір тексту
    ),
    shape = RoundedCornerShape(8.dp)  // Заокруглення кутів
) {
    Text(
        "Reset Round",
        style = MaterialTheme.typography.titleMedium,  // Розмір тексту
        modifier = Modifier.padding(horizontal = 8.dp)  // Відступи тексту
    )
}

// Анімація появи кнопки Next Round
AnimatedVisibility(
    visible = gameStatus.isNotEmpty(),  // Показуємо тільки після закінчення раунду
    enter = fadeIn() + expandHorizontally(),  // Плавна поява
    exit = fadeOut() + shrinkHorizontally()  // Плавне зникнення
) {
    Button(/* ... */)
}
```
### Вплив на UX:
- Висота кнопок 48.dp відповідає Material Design guidelines
- Тінь 4.dp створює візуальну глибину
- Тактильний відгук покращує зворотний зв'язок
- Анімації з'являються тільки коли потрібні

## 5. Додавання діалогу статистики
### Конкретні зміни:
```kotlin
// Діалог статистики
Dialog(onDismissRequest = { showScoreDialog = false }) {
    Surface(
        modifier = Modifier
            .fillMaxWidth()
            .padding(16.dp)
            .shadow(8.dp, RoundedCornerShape(16.dp)),  // Тінь для 3D ефекту
        shape = RoundedCornerShape(16.dp),  // Заокруглення кутів
        color = MaterialTheme.colorScheme.surface  // Фоновий колір
    ) {
        Column(
            modifier = Modifier.padding(16.dp),
            horizontalAlignment = Alignment.CenterHorizontally
        ) {
            Text(
                "Game Statistics",
                style = MaterialTheme.typography.headlineSmall,  // Заголовок
                modifier = Modifier.padding(bottom = 16.dp)
            )
            Text(
                "Player X: $xScore wins",
                style = MaterialTheme.typography.titleMedium,
                color = MaterialTheme.colorScheme.primary,  // Колір для X
                modifier = Modifier.padding(bottom = 8.dp)
            )
            Text(
                "Player O: $oScore wins",
                style = MaterialTheme.typography.titleMedium,
                color = MaterialTheme.colorScheme.secondary,  // Колір для O
                modifier = Modifier.padding(bottom = 8.dp)
            )
            Text(
                "Total rounds: $roundNumber",
                style = MaterialTheme.typography.titleMedium,
                modifier = Modifier.padding(bottom = 16.dp)
            )
            Button(
                onClick = { showScoreDialog = false },
                modifier = Modifier
                    .fillMaxWidth()
                    .shadow(4.dp, RoundedCornerShape(8.dp))
            ) {
                Text("Close")
            }
        }
    }
}
```
### Вплив на UX:
- Тінь 8.dp створює ефект "спливаючого" вікна
- Відступи 16.dp та 8.dp створюють збалансований дизайн
- Кольори відповідають загальній темі гри
- Кнопка "Close" займає всю ширину для зручності

## 6. Покращення відображення рахунку
### Конкретні зміни:
```kotlin
// Картка рахунку
Row(
    modifier = Modifier
        .fillMaxWidth()
        .padding(bottom = 16.dp)
        .shadow(4.dp, RoundedCornerShape(16.dp))  // Тінь для 3D ефекту
        .clip(RoundedCornerShape(16.dp))
        .background(MaterialTheme.colorScheme.surface)
        .padding(16.dp),
    horizontalArrangement = Arrangement.SpaceEvenly  // Рівномірний розподіл
) {
    Column(horizontalAlignment = Alignment.CenterHorizontally) {
        Text(
            "Player X",
            style = MaterialTheme.typography.titleMedium,
            color = MaterialTheme.colorScheme.primary
        )
        Text(
            xScore.toString(),
            style = MaterialTheme.typography.headlineMedium,  // Більший розмір для рахунку
            color = MaterialTheme.colorScheme.primary
        )
    }
    Column(horizontalAlignment = Alignment.CenterHorizontally) {
        Text(
            "Round $roundNumber",
            style = MaterialTheme.typography.titleMedium
        )
    }
    Column(horizontalAlignment = Alignment.CenterHorizontally) {
        Text(
            "Player O",
            style = MaterialTheme.typography.titleMedium,
            color = MaterialTheme.colorScheme.secondary
        )
        Text(
            oScore.toString(),
            style = MaterialTheme.typography.headlineMedium,
            color = MaterialTheme.colorScheme.secondary
        )
    }
}
```
### Вплив на UX:
- Тінь 4.dp створює візуальну глибину
- Розмір шрифту headlineMedium для рахунку покращує читабельність
- Рівномірний розподіл елементів створює збалансований вигляд
- Кольори відповідають символам гравців

## 7. Додавання тактильного відгуку
### Конкретні зміни:
```kotlin
// Отримання доступу до тактильного відгуку
val haptic = LocalHapticFeedback.current

// Використання в обробці ходу
clickable {
    if (field[index] == "_" && gameStatus.isEmpty()) {
        haptic.performHapticFeedback(HapticFeedbackType.TextHandleMove)  // Тактильний відгук
        // ... логіка ходу ...
    }
}
```
### Вплив на UX:
- Тактильний відгук підтверджує успішне натискання
- Використовується TextHandleMove для легкого відгуку
- Відгук спрацьовує тільки при валідному ході

## 8. Покращення анімацій
### Конкретні зміни:
```kotlin
// Анімація статусу гри
val statusScale by animateFloatAsState(
    targetValue = if (gameStatus.isNotEmpty()) 1.1f else 1f,  // Масштаб 1.1 при перемозі
    animationSpec = spring(
        dampingRatio = Spring.DampingRatioMediumBouncy,  // Пружна анімація
        stiffness = Spring.StiffnessLow  // Повільна анімація
    )
)

// Анімація ходу
val scale by animateFloatAsState(
    targetValue = if (field[index] != "_") 1.1f else 1f,
    animationSpec = spring(
        dampingRatio = Spring.DampingRatioMediumBouncy,
        stiffness = Spring.StiffnessLow
    )
)
```
### Вплив на UX:
- Масштаб 1.1f створює помітний, але не надто агресивний ефект
- Пружна анімація додає "живості" інтерфейсу
- Повільна анімація не відволікає від гри

## 9. Покращення адаптивності
### Конкретні зміни:
```kotlin
// Адаптивні розміри
Modifier
    .fillMaxWidth()  // Заповнення доступної ширини
    .padding(16.dp)  // Стандартний відступ
    .weight(1f)  // Рівний розподіл простору

// Адаптивні відступи
Modifier.padding(
    horizontal = 16.dp,  // Горизонтальний відступ
    vertical = 8.dp  // Вертикальний відступ
)
```
### Вплив на UX:
- fillMaxWidth() забезпечує використання всього доступного простору
- weight(1f) створює рівномірний розподіл елементів
- Стандартні відступи забезпечують консистентний вигляд

## 10. Покращення кольорової схеми
### Конкретні зміни:
```kotlin
// Використання кольорів Material Design 3
MaterialTheme.colorScheme.primary  // Основний колір
MaterialTheme.colorScheme.secondary  // Додатковий колір
MaterialTheme.colorScheme.surface  // Фоновий колір
MaterialTheme.colorScheme.background  // Колір фону
MaterialTheme.colorScheme.primaryContainer  // Контейнер для основного кольору
MaterialTheme.colorScheme.onPrimaryContainer  // Колір тексту на контейнері
```
### Вплив на UX:
- Системні кольори забезпечують консистентний вигляд
- Контрастні кольори покращують читабельність
- Адаптивні кольори працюють в обох темах

## Технічні зміни
### Конкретні зміни:
```kotlin
// Нові імпорти
import androidx.compose.animation.*  // Анімації
import androidx.compose.animation.core.*  // Базові анімації
import androidx.compose.foundation.BorderStroke  // Рамки
import androidx.compose.ui.draw.shadow  // Тіні
import androidx.compose.ui.hapticfeedback.HapticFeedbackType  // Тактильний відгук
import androidx.compose.ui.platform.LocalHapticFeedback  // Доступ до тактильного відгуку

// Нові стани
var isDarkTheme by remember { mutableStateOf(false) }  // Стан теми
var isFirstMove by remember { mutableStateOf(true) }  // Стан першого ходу
var showScoreDialog by remember { mutableStateOf(false) }  // Стан діалогу
```
### Вплив на код:
- Нові імпорти забезпечують доступ до необхідних функцій
- Стани керують поведінкою інтерфейсу
- Модифікатори оптимізовані для кращої продуктивності 