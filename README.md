# Краткие решения для виджетов Visiology

*Важно!*

При работе с кодом есть две части для редактирования виджетов:
1. До инициализации виджета
2. После Инициализации виджета

Пример инициализации виджета из библиотеки Highcharts:
```javascript
// место до инициализации

Highcharts.chart({
    chart: w.general,
    ...
});

// меесто после инициализации
```

Инициализация таблиц: 
```javascript
TableRender({
    table: w.general,
    ...
});
```

Перед вставкой заготовленного кода важно помещать его в указанное место, так как в противном случае код не будет работать корректно.

Обратите внимание: все операции, связанные с подсчетом или заменой данных, должны выполняться **до инициализации виджетов**. То есть данные изменяются до их передачи в виджет, до начала его отрисовки. Изменения, касающиеся внешнего вида виджетов (например, закрепление шапки таблицы, изменение фона у конкретных ячеек), выполняются **после их отрисовки**.

---

# 1. Редактирование таблиц
Код для работы с обычными таблицами Visiology.

## 1.1 Вывод строки с итогами 
Подсчет суммы столбиков *(до инициализации)*:
```javascript
const summColObj = {rowNames: ['Итого']}

w.data.records.forEach(record => {
    if (w.data.records.length > 1) {
        for (const [key, value] of Object.entries(record)) {
          if (key.includes('column')) {
              const summ = w.data.records.reduce((a, b) => {
                  const newA = typeof a === 'object' ? a[key] : a
                  const newB = typeof b === 'object' ? b[key] : a
                    return newA + newB
              })
              summColObj[key] = summ.toFixed(2);
          }
        }
    } else {
        for (const [key, value] of Object.entries(record)) {
          if (key.includes('column')) {
              summColObj[key] = value.toFixed(2);
          }
        }
    }
})

w.data.records.push(summColObj)
```
Стили для итоговой строки *(после инициализации)*:
```javascript
$('#table-' + w.general.renderTo + ' tbody tr:last-of-type').css({
    fontWeight: 'bold',
    background: '#E0E4E7'
});
```

## 1.2 Измененние заголовков таблицы (работает только с измерениями)
Замена первых трех заголовков. Количество при необходимости можно изменить, увеличивая цифры в квадратных скобках *(до инициализации)*:
```javascript
w.data.columns[0].captions = ['Заголовок 1'];
w.data.columns[1].captions = ['Заголовок 2']; 
w.data.columns[2].captions = ['Заголовок 3']; 
```
