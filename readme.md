AMS - Automatic management system

(обновлено 08.07.2024)

Это система автоматической заправки-выгрузки филамента для 3д принтера. Разрабатывалась исключительно для себя, но так как появился интерес у общественности, решено было выложить для всех.
Для загрузки катушка кладется на ролики, вставляется пластик и по кнопке/макросу LOAD_FILAMENT пластик загружается в голову.
При выгрузке по кнопке/макросу UNLOAD_FILAMENT пластик выгружается обратно до мотора, при этом катушка наматывает обратно пластик.

Работает автозагрузка при старте, автовыгрузка при окончании печати, автозамена при окончании пластика, автозамена при многоцветной печати.

Из электроники система состоит из 2 моторов в фидерах и датчика филамента на голове, ВСЕ.
В ams.step можно посмотреть все устройство.
Крепление на профиль 3030 (RatRig)
Из фидеров трубки приходят в голову через splitter 2-IN-1 (4-IN-1)
Прошивка на основе HappyHare для Klipper. Совместима с последней версией клиппера на 27.06.2024.

Система ERCF не подошла, так как более сложная и катушка не наматывает пластик. Была задача все упростить и за основу взять AMS от BambuLab.
Очень много лишнего и мусора везде, но работает.

Для сборки требуется:

1. Комплект шестеренок, штифтов и пружинки от BMG/клона фидера
2. 2 практически любых шаговых мотора 42х42мм, 2 свободных места на плате управления с драйверами
3. Винты М3, трубки
4. Датчик филамента на голове
5. Размер подшипников для роликов можно посмотреть в step, подходят от Flying Bear Reborn V1 от верхних креплений валов Z, либо переделать на свои.

Установка:

1. Печатаем всю конструкцию из ams.step (или из папки stl), фидер из любого материала кроме PLA, остальное можно и из PLA если не будет в термокамере.
2. Копируем из папки extras в папку extras клиппера 2 файла.
3. Копируем папку config/mmu в папку config клиппера все.

Настройка:

1. В printer.cfg в конец добавить строки:

[include mmu/\*.cfg]

[include mmu/base/\*.cfg]

2. В mmu/base/mmu_hardware.cfg настроить секции stepper_mmu_gear (и tmc2209 stepper_mmu_gear), stepper_mmu_selector (b tmc2209 stepper_mmu_selector) для моторов
3. Проверить в вашем конфиге секцию filament_switch_sensor для датчика филамента на голове. Она должна называться [filament_switch_sensor extruder_sensor].
4. В mmu/filament_management.cfg в секции [gcode_macro LOAD_FILAMENT] внести variable_length1 и variable_length2 - это расстояние для выгрузки филамента от датчика до 2-3 см недоезжая до шестеренок мотора, можно настроить в процессе работы. Координаты в командах G0 X475 ИЗМЕНИТЬ НА СВОИ!!! Это координаты движения головы через прочистку после заправки пластика.
5. В mmu/macros.cfg в секции [gcode_macro _POWER_OFF_PRINTER] изменить имя устройства "printer" на ваше, это макрос для автовыключения принтера (если это необходимо).
6. В mmu/print_management.cfg проверить все макросы, особенно [START_PRINT] и [END_PRINT]. ИЗМЕНИТЬ КООРДИНАТЫ ПАРКОВКИ ОКОЛО ПРОЧИСТКИ X490 Y497 НА ВАШИ!!!

Выбор пластика происходит перед стартом печати с помощью команд T1 или T2, а также стандартные кнопки T1 и T2, которые появятся в интерфейсе Mainsail. Если пластик заканчивается в процессе печати, загружается следующий из соседней катушки.

Стартовый g-code в слайсере (OrcaSlicer):  
START_PRINT EXTRUDER_TEMP=[nozzle_temperature_initial_layer] BED_TEMP=[bed_temperature_initial_layer_single]

Конечный g-code в слайсере:  
END_PRINT

g-code, выполняемый при смене прутка, для многоцветной печати (OrcaSlicer):  
PARK  
UNLOAD_FILAMENT T=0  
T{next_extruder+1}  
LOAD_FILAMENT T={new_filament_temp} MOVECLEARAFTER=1  
RESTORE_GCODE_STATE name=PARK_STATE MOVE=1 MOVE_SPEED=700  

Вроде все. Мог что-то забыть.

PS Вся эта сборка предоставляется AS IS (КАК ЕСТЬ). ПОЛНОСТЬЮ под вашу ответственность и тд и тп.