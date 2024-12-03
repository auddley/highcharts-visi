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

Обратите внимание: все операции, связанные с подсчетом или заменой данных, должны выполняться **до инициализации виджетов**. То есть данные изменяются до их передачи в виджет, до начала его отрисовки.  
Изменения, касающиеся внешнего вида виджетов (например, закрепление шапки таблицы, изменение фона у конкретных ячеек), выполняются **после их отрисовки**.

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

## 1.2 Изменение заголовков таблицы (работает только с измерениями)
Замена первых трех заголовков. Количество при необходимости можно изменить, увеличивая цифры в квадратных скобках *(до инициализации)*:
```javascript
w.data.columns[0].captions = ['Заголовок 1'];
w.data.columns[1].captions = ['Заголовок 2']; 
w.data.columns[2].captions = ['Заголовок 3']; 
```

## 1.3 Замена значений <Пусто> на строку 'Нет данных'
«Нет данных» можно заменить на что угодно. Чтобы оставить пустую ячейку без текста, необходимо убрать весь текст и оставить пустые кавычки ' ' *(до инициализации)*:
```javascript
w.data.rowNames.map(el => {
    el.map((_el, _index) => {
        if (_el === '<Пусто>') {
            el[_index] = 'Нет данных'
        }
    })
})
```

## 1.4 Закреп верхней шапки таблицы
Сохранение положения заголовков таблицы при вертикальном скролле *(после инициализации)*:
```javascript
const mainTable = document.querySelector('#widget-' + w.general.renderTo + ' .va-widget-body');

mainTable.querySelector('thead tr').style.position = 'sticky'
mainTable.querySelector('thead tr').style.top = '0'
mainTable.querySelector('thead tr').style.background = 'white' // важно задать фоновый цвет, иначе текст будет накладываться
```

## 1.5 Закреп боковой шапки таблицы
Сохранение положения левых заголовков таблицы при горизонтальном скролле *(после инициализации)*:
```javascript
const mainTable = document.querySelector('#widget-' + w.general.renderTo + ' .va-widget-body');

mainTable.querySelectorAll('tbody tr td:first-child').forEach(el => {
    el.style.position = 'sticky'
    el.style.left = '0'
    el.style.background = 'white'
})
```

## 1.6 Редактирование размера столбиков таблицы
Размер столбиков меняется через ширину заголовков таблицы *(после инициализации)*:
```javascript
const mainTable = document.querySelector('#widget-' + w.general.renderTo + ' .va-widget-body');

// редактирование шапки, изменение размера столбиков
mainTable.querySelectorAll('thead tr th').forEach((el, index) => {
    // 1 столбик
    if (index === 0) {
        el.style.width = '200px'
    }
    
    // 2 столбик
    if (index === 1) {
        el.style.width = '100px'
    }
    
    // 3 столбик
    if (index === 2) {
        el.style.width = '150px'
    }
    
    // 4 столбик
    if (index === 3) {
        el.style.width = '150px'
    }

    // и т.д.
})
```

## 1.7 Скрытие иконок сортировки в таблице (стрелочек)
*(после инициализации)*:
```javascript
const mainTable = document.querySelector('#widget-' + w.general.renderTo + ' .va-widget-body');

mainTable.querySelectorAll('thead tr th').forEach((el, index) => {
    if (el.querySelector('.fa.fa-sort')) {
        el.querySelector('.fa.fa-sort').style.display = 'none';
    }
})
```

## 1.8 Скрытие боковых границ ячеек в таблице
Скрытие границ в шапке и в строках таблицы *(после инициализации)*:
```javascript
const mainTable = document.querySelector('#widget-' + w.general.renderTo + ' .va-widget-body');

// скрытие в шапке
mainTable.querySelectorAll('thead tr th').forEach((el, index) => {
    el.style.borderLeft = 'none';
    el.style.borderRight = 'none';
})

// скрытие в строках
mainTable.querySelectorAll('tbody tr').forEach((el, index) => {
    el.querySelectorAll('td').forEach((_el, _index) => {
        _el.style.borderLeft = 'none';
        _el.style.borderRight = 'none';
    })
})
```

## 1.9 Скрытие определенного столбика таблице
Пример скрытия 4-го столбца таблицы. При необходимости можно изменить число в проверке (index === 9) и (_index === 9), чтобы скрыть, например, 10-й столбец. Заметьте, что индекс в проверке всегда на 1 меньше номера нужного столбца.

Для скрытия нескольких столбцов продублируйте выражение if {}, указав соответствующие индексы столбцов *(после инициализации)*:
```javascript
const mainTable = document.querySelector('#widget-' + w.general.renderTo + ' .va-widget-body');

// скрытие в шапке
mainTable.querySelectorAll('thead tr th').forEach((el, index) => {
    // 4 столбик
    if (index === 3) {
        el.style.display = 'none'
    }
})

// скрытие в строках
mainTable.querySelectorAll('tbody tr').forEach((el, index) => {
    el.querySelectorAll('td').forEach((_el, _index) => {
        // 4 столбик
        if (_index === 3) {
            _el.style.display = 'none'
        }
    })
})
```

## 1.10 Добавление чересполосицы в таблицу (каждая вторая строка голубая)
*(после инициализации)*:
```javascript
const mainTable = document.querySelector('#widget-' + w.general.renderTo + ' .va-widget-body');

mainTable.querySelectorAll('tbody tr').forEach((el, index) => {
    el.querySelectorAll('td').forEach((_el, _index) => {
        if (index % 2 !== 0) {
            _el.style.background = '#EFF7FA'
        }
    })
})
```

## 1.11 Готовый код для форматирования таблицы в общий дизайн
Заменить полностью код виджета, инициализация тут присутствует:
```javascript
w.data.columns[0].captions = ['Номер'];
w.data.columns[1].captions = ['Доработка']; 

w.data.rowNames.map(el => {
    el.map((_el, _index) => {
        if (_el === '<Пусто>') {
            el[_index] = ' '
        }
    })
})

TableRender({
    table: w.general,
    style: w.style,
    columns: w.data.columns,
    records: w.data.records,
    editMask: w.data.editMask,
    rowNames: w.data.rowNames,
    colNames: w.data.colNames,
    showToolbar: false
});

const mainTable = document.querySelector('#widget-' + w.general.renderTo + ' .va-widget-body');

// редактирование шапки, изменение размера столбиков
mainTable.querySelectorAll('thead tr th').forEach((el, index) => {
    el.style.borderLeft = 'none';
    el.style.borderRight = 'none';

    if (el.querySelector('.fa.fa-sort')) {
        el.querySelector('.fa.fa-sort').style.display = 'none';
    }
    
    // 1 столбик
    if (index === 0) {
        el.style.width = '110px'
    }
    
    // 2 столбик
    if (index === 1) {
        el.style.width = '100px'
    }
    
    // 3 столбик
    if (index === 2) {
        el.style.width = '150px'
    }
})

// закрепление шапки таблицы
mainTable.querySelector('thead tr').style.position = 'sticky'
mainTable.querySelector('thead tr').style.top = '0'
mainTable.querySelector('thead tr').style.background = 'white'

// удаление лишнего столбика, окрашивание строк в голубой
mainTable.querySelectorAll('tbody tr').forEach((el, index) => {
    el.querySelectorAll('td').forEach((_el, _index) => {
        _el.style.borderLeft = 'none';
        _el.style.borderRight = 'none';
        
        if (_index !== 0) {
            _el.style.textAlign = 'center';
        }
    })
})

```

---

# 2. Редактирование диаграмм Highcharts
## 2.1 Скрытие белой обводки вокруг цифр
![image](https://github.com/user-attachments/assets/11ad3d09-741d-4e43-a484-5cc23f9d4c12)
![image](https://github.com/user-attachments/assets/94780093-2bc8-4902-a07a-787dc19042f7)

*(до инициализации)*:
```javascript
w.plotOptions.series.dataLabels.style.textOutline = 'none'
```

---

# 3. Кастомные виджеты
## 3.1 Скрытие (редактирование) панели навигации
```javascript
/*
Данный код добавить в любой виджет на КАЖДОМ листе дашборда (можно специально выносить за экран виджет с этим кодом). 
Т.к. стили применяются только при выполнении кода виджета, 
если добавить виджет с таким кодом например только на первом листе,
то при открытии дашборда по ссылке на второй лист - наш код с виджета на первом листе не выполнится
и стили для переключателя между листами в таком случае не применятся
*/

// Переменная со стилями
const listStyles = document.createElement('style')

// Стили (Для некоторых css свойств необходимо использовать - !important)
listStyles.innerHTML = `
    #va-sheets-navigator-container {
        display: none;
    } 
`

// Заносим стили в DOM
document.body.appendChild(listStyles)
```
