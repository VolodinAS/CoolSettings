# CoolSettings
Класс на Python для сохранения настроек Вашего приложения

## Как работать?

### 1. Первым делом, импортируем библиотеку
```python
from CoolSettings import CoolSettings
```

### 2. Далее - инициируем словарь с дефолтными настройками:
```python
default_settings_parameters_description = {
    'название_переменной': ['описание_переменной', 'значение_переменной'],
    'settings1': ['This is settings 1', 12345],
    'settings2': ['This is settings ', True],
    'settings3': ['This is settings 3', False],
    'settings4': ['This is settings 4', 'string'],
    'settings5': ['This is settings 5', {'key':'key1', 'value': 'value1'}],
}
```
Как видите, настройки могут иметь разный тип

### 3. Иницируем работу класса:
```python
CS = CoolSettings(
                    filename='указываем_путь_к_файлу',                              # путь к файлу
                    default_parameters=default_settings_parameters_description,     # словарь с дефолтными параметрами
                    debug=True)                                                     # debug в консоль
```

### 4. Получаем настройки:
```python
JSON_Settings = CS.settings
for item in JSON_Settings.items():
    print(item)
```
##### Вывод:
```
('название_переменной', 'значение_переменной')
('settings1', 12345)
('settings2', True)
('settings3', False)
('settings4', 'string')
('settings5', {'key': 'key1', 'value': 'value1'})
```

### 5. Доступ к настройкам
Получив данные из json-файла, Вы можете работать с этими настройками, как с обычным словарём:
```python
JSON_Settings['settings6'] = 'very_new_value'
for item in JSON_Settings.items():
    print(item)
```
#### Вывод:
```python
('название_переменной', 'значение_переменной')
('settings3', False)
('settings4', 'string')
('settings5', {'key': 'key1', 'value': 'value1'})
('settings6', 'very_new_value')                     # значение settings 6 поменялось
```

### 5. Сохранить настройки в файл
```python
CS.save()
```

### 6. Удалить ненужную переменную настройки
Удаление происходит только если Вы удалите ненужное значение из словаря дефолтных значений (для подробностей - читайте далее)

## Основное отличие от других библиотек для хранения настроек

Основное отличие - динамическое удаление/добавление настроек при изменении "default_parameters". Посмотрим пример:
```python
default_settings_parameters_description = {
    'название_переменной': ['описание_переменной', 'значение_переменной'],
    # 'settings1': ['This is settings 1', 12345],                           # удалим из настроек settings 1
    # 'settings2': ['This is settings ', True],                             # удалим из настроек settings 2
    'settings3': ['This is settings 3', False],
    'settings4': ['This is settings 4', 'string'],
    'settings5': ['This is settings 5', {'key':'key1', 'value': 'value1'}],
    'settings6': ['New settings 5', 'new_value'],                           # добавим новую настройку settings 6
}

CS = CoolSettings(
                    filename='указываем_путь_к_файлу',                              # путь к файлу
                    default_parameters=default_settings_parameters_description,     # словарь с дефолтными параметрами
                    debug=True)                                                     # debug в консоль
                    
JSON_Settings = CS.settings
for item in JSON_Settings.items():
    print(item)
```
##### Вывод:
```python
('название_переменной', 'значение_переменной')
('settings3', False)
('settings4', 'string')
('settings5', {'key': 'key1', 'value': 'value1'})
('settings6', 'new_value')
```

Заглянув повыше, Вы увидите лог обновления настроек:
```python
EXISTS:  название_переменной
NOT FOUND, DELETED: settings1
NOT FOUND, DELETED: settings2
EXISTS:  settings3
EXISTS:  settings4
EXISTS:  settings5

OLD:  название_переменной
OLD:  settings3
OLD:  settings4
OLD:  settings5
NEW:  settings6
```

За этот вывод отвечает функция "check_preferences()". Она состоит из двух частей. Первая - сверяет json-данные из файла с дефолтным словарем и удаляет те, которые отсутствуют в дефолтных настройках:
```python
EXIST - переменная существует - переменная останется в списке, так как она существует в дефолтном словаре настроек
NOT FOUND, DELETED - переменная удалена, так как она отсутствует в дефолтном словаре настроек
```

Вторая часть, наоборот, проверяет json-словарь настроек на наличие новых переменных из дефолтного словаря настроек и подставляет в настройку, присваивая значение по дефолту
```python
OLD - старая переменная, оставляем
NEW - новая переменная - добавляем.
```

Если Вы запустите скрипт еще раз, то лог будет следующим:
```python
# ничего лишнего
EXISTS:  название_переменной
EXISTS:  settings3
EXISTS:  settings4
EXISTS:  settings5
EXISTS:  settings6

# и ничего нового
OLD:  название_переменной
OLD:  settings3
OLD:  settings4
OLD:  settings5
OLD:  settings6

('название_переменной', 'значение_переменной')
('settings3', False)
('settings4', 'string')
('settings5', {'key': 'key1', 'value': 'value1'})
('settings6', 'new_value')
```
