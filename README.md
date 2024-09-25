# Кросс-компиляция

### Подготовка

- Убедимся, что все необходимые программы установлены в системе ([ссылка](https://www.kernel.org/doc/html/latest/process/changes.html#));
- Склонируем официальный репозиторий Linux ([ссылка](https://github.com/torvalds/linux));
- Если в репозитории ранее что-то собиралось, необходимо выполнить команду `make distclean`.

### Сборка

- `ARCH=arm make defconfig` - создадим дефолтную конфигурацию под архитектуру ARM;

  ![image](https://github.com/user-attachments/assets/b247aa5b-88c5-430f-a3aa-71c79c650118)
- `ARCH=arm make menuconfig` - при необходимости можно изменить параметры конфигурации;
- `ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- make -j<число ядер> zImage` - соберем ядро под заданную архитектуру;

  ![image](https://github.com/user-attachments/assets/529759d1-96ee-480c-a7ed-578c5a6d661f)

  > После сборки увидим надпись `Kernel: arch/arm/boot/zImage is ready`.

- `ARCH=arm make -j<число ядер> dtbs` - соберем device tree blob (dtb) файлы, чтобы затем выбрать нужный. Это поможет эмулятору понять, какой функционал универсального ядра использовать, т.к. ядро умеет работать с разными платами.

  ![image](https://github.com/user-attachments/assets/cc1edbca-1d87-4923-8b1d-5ecf38989837)

### Тестирование

- `QEMU_AUDIO_DRV=none qemu-system-arm -M vexpress-a9 -kernel zImage -dtb vexpress-v2p-ca9.dtb -append "console=ttyAMA0" -nographic` - запустим виртуальную машину с эмулированной ARM платформой Versatile Express Cortex-A9, загрузим ядро из файла zImage, используя файл конфигурации устройства vexpress-v2p-ca9.dtb, направим вывод консоли на виртуальный последовательный порт ttyAMA0 и запустим всё это в текстовом режиме;

  ![image](https://github.com/user-attachments/assets/a5e4f089-491e-4617-bb50-7ec037457223)

> [!TIP]
> Список поддерживаемых плат можно вывести командой `qemu-system-arm -machine help`.

- После запуска ядра увидим ошибку `Kernel panic`. Это говорит о том, что ядро запустилось, но не смогло найти корневую файловую систему и процесс init в ней.

  ![image](https://github.com/user-attachments/assets/1f283373-1b37-40f5-af3c-d6dc5c3f2a45)

Таким образом, ядро было успешно собрано и запущено. Сборка корневой файловой системы происходит в [задании 18](https://github.com/EltexEmbeddedC/root-filesystem).
