<div class="noprint" style="margin-left: 100px">
<input type="checkbox" id="animationToggle2" class="hidden-checkbox">
<label for="animationToggle2" class="toggle-button">Реплика 2</label>
<input type="checkbox" id="animationToggle3" class="hidden-checkbox">
<label for="animationToggle3" class="toggle-button">Реплика 3</label>
<input type="checkbox" id="animationToggle3Alt" class="hidden-checkbox">
<label for="animationToggle3Alt" class="toggle-button">EC 2+1</label>

<div class="container" id="animationContainer">
        <!-- Источник данных (клиент) -->
        <div class="storage source"><p class="textv">ВМ</p>
         <div class="inner-client">гостевая ФС</div>
        </div>
        <div class="commutator"><p class="textc">ethernet коммутатор</p></div>
        <!-- Три сервера хранилища -->
        <div class="storage server1">
         <p class="textb">сервер 1</p>
          <div class="storage client">
           <div class="storage qcow2">qcow2</div>
            <p class="textk">клиент</p>
          </div>
          <div class="storage mds">mds1</div>
         <div class="storage storage-1">Диск CS#1</div>
        </div>
        <!-- сервер 2 и 3 -->
        <div class="storage server2">
         <div class="storage mds">mds2</div>
         <p class="textb">сервер 2</p>
          <div class="storage storage-2">Диск CS#2</div>
        </div>
        <div class="storage server3">
         <div class="storage mds">mds3</div>
         <p class="textb">сервер 3</p>
         <div class="storage storage-3">Диск CS#3</div>
        </div>
        <!-- Кубики (данные) -->
        <div class="cube cube-1"></div>
        <div class="cube cube-2"></div>
        <div class="cube cube-3"></div>
        <div class="connection-line line-1"></div>
        <div class="connection-line line-2"></div>
        <div class="connection-line line-3"></div>
        <div class="connection-line line-4"></div>
</div>
</div>
<div>
</br>
</br><p style="text-align: center">
<b>Анимационная схема, показывает минимальную </br>конфигурацию кластера Р-хранилища: </b></p>
</br>
</br>
<p style="text-align: justify">
1.<b>ВМ с гостевой фс</b> - это виртуальная машина внутри которой установлена гостевая операционная система с файловой системой. 
</br>
</br>
2.<b>qcow2</b> - это файл или образ виртуального диска виртаульной машины. 
</br>
</br>
3.<b>mds1-3</b> - это служба метаданных, которая содержит карту расположения блоков и контроллирует кворум.
На каждом сервер по одной такой службе.  
</br>
</br>
4.<b>Диск CS#1-3</b> - это служба хранения блоков, каждая служба управляет одним диском. 
</br>
</br>
5.<b>клиент</b> - служба или точка монтирования кластер Р-хранилища в которой хранятся файлы или 
виртулаьные диски виртаульным машин ввиде файлов. Каждый файл расщепляется на блоки и их копии при операции записи. 
Распределение блоков по серверам и их дискам осуществляется после того как службы mds согласуют карту и предоставят ее клиенту. 
</br>
</br>
6.<b>ethernet коммутатор</b> - это сетевой коммутатор, который связывает по сети интерконеккта три сервера,
образуя единую шину для передачи блоков и их копий данных. 
</br>
</br>
7.Над схемой размещены кнопки для включения определенного вида избыточности.Всего три вида, но на самом деле их больше и 
в настройках платформы можно самому добавлять новые режимы, описанным на главной старницы данной инструкции.  
</p>






</div>

<script>
    // Элементы управления
    const toggle2 = document.getElementById('animationToggle2');
    const toggle3 = document.getElementById('animationToggle3');
    const toggle3Alt = document.getElementById('animationToggle3Alt');
    const cubes = document.querySelectorAll('.cube');
    // элементы для анимации импульса синхронизации mds
    const pulseElements = {
      client: document.querySelector('.storage.client'),
      commutator: document.querySelector('.commutator'),
      mds1: document.querySelector('.server1 .mds'),
      mds2: document.querySelector('.server2 .mds'),
      mds3: document.querySelector('.server3 .mds')
    };
    
    // Конфигурация анимаций
    const animationConfig = {
  rep2: { // Реплика 2
    cubes: [0, 1], // Красный (0) и оранжевый (1)
    sequences: [
      // Итерация 1: Красный → CS#1, Оранжевый → CS#2
      { targets: [0, 1], storage: [1, 2] },
      // Итерация 2: Красный → CS#1, Оранжевый → CS#3
      { targets: [0, 1], storage: [1, 3] },
      // Итерация 3: Красный → CS#1, Оранжевый → CS#2
      { targets: [0, 1], storage: [1, 2] }
    ],
    colors: ['#e74c3c', '#f39c12'],
    // Новая настройка для смещения кубиков в хранилищах
    offsets: {
      1: { x: 0, y: 0 },    // CS#1 - базовое положение
      2: { x: 180, y: 150 }, // CS#2
      3: { x: 380, y: 150 }  // CS#3
    }
  },
      rep3: { // Реплика 3
        cubes: [0, 1, 2], // Все кубики
        sequences: [
          { targets: [0, 1, 2], storage: [1, 2, 3] },
          { targets: [0, 1, 2], storage: [1, 3, 2] },
          { targets: [0, 1, 2], storage: [1, 2, 3] }
        ],
        colors: ['#e74c3c', '#f39c12', '#f3ec12']
      },
      ec21: { // EC 2+1
        cubes: [0, 1, 2],
        sequences: [
          { targets: [0, 1, 2], storage: [1, 2, 3], colors: ['#47ab19','#47ab19','#a2edf5'] },
          { targets: [0, 1, 2], storage: [1, 3, 2], colors: ['#47ab19','#47ab19','#a2edf5'] },
          { targets: [0, 1, 2], storage: [1, 2, 3], colors: ['#47ab19','#47ab19','#a2edf5'] }
        ]
      }
    };

    // Хранилище для накопленных кубиков
    const accumulatedCubes = {
      1: [], // CS#1
      2: [], // CS#2
      3: []  // CS#3
    };
    
    let currentMode = null;
    let animationTimeout = null;
    let currentAnimationId = 0; // Новая переменная для контроля анимаций
    

    // Изначально все анимации выключены
    resetAllCubes();
    
    // Обработчик для кнопки "Реплика 2" (2 кубика)
    toggle2.addEventListener('change', function() {
        if (this.checked) {
          currentAnimationId++;  // Инвалидируем предыдущие анимации
          // Сбрасываем предыдущую анимацию
            resetAllCubes();
           //выключаем лишние кнопки
            toggle3.checked = false;
            toggle3Alt.checked = false;
            clearTimeout(animationTimeout); //для очищения анимации от других кнопок после выключения 
            // Запускаем новую анимацию
        startAnimation('rep2');
    } else {
        currentAnimationId++;  // Инвалидируем при выключении
        // При отключении кнопки сбрасываем анимацию
        resetAllCubes();
        clearTimeout(animationTimeout);
    }
    updateButtonStates();
});
    
    // Обработчик для кнопки "Реплика 3" (3 кубика)
   toggle3.addEventListener('change', function() {
    if (this.checked) {
        currentAnimationId++;
        resetAllCubes();
        toggle2.checked = false;
        toggle3Alt.checked = false;
        clearTimeout(animationTimeout); 
        startAnimation('rep3');
    } else {
        currentAnimationId++;
        resetAllCubes();
    }
    updateButtonStates();
});

toggle3Alt.addEventListener('change', function() {
    if (this.checked) {
        currentAnimationId++;
        resetAllCubes();
        toggle2.checked = false;
        toggle3.checked = false;
       clearTimeout(animationTimeout); 
        startAnimation('ec21');
    } else {
        currentAnimationId++;
        resetAllCubes();
    }
    updateButtonStates();
});
// Новая функция для анимации импульса
function animatePulse(from, to, callback) {
  const pulse = document.createElement('div');
  pulse.className = 'pulse-effect';
  document.getElementById('animationContainer').appendChild(pulse);
  
  // Позиционируем импульс
  const fromRect = from.getBoundingClientRect();
  const toRect = to.getBoundingClientRect();
  const containerRect = document.getElementById('animationContainer').getBoundingClientRect();
    
  const startX = fromRect.left - containerRect.left + fromRect.width/2;
  const startY = fromRect.top - containerRect.top + fromRect.height/2;
  const endX = toRect.left - containerRect.left + toRect.width/2;
  const endY = toRect.top - containerRect.top + toRect.height/2;
    
  pulse.style.left = startX + 'px';
  pulse.style.top = startY + 'px';
  
  // Анимация
  pulse.animate([
        { transform: `translate(0, 0)`, opacity: 1 },
        { transform: `translate(${endX-startX}px, ${endY-startY}px)`, opacity: 0 }
    ], {
        duration: 400,
        easing: 'ease-in-out'
    }).onfinish = () => {
        pulse.remove();
        if(callback) callback();
  };
}
function startAnimation(mode) {
  // Сначала анимируем импульсы синхронизации
    animatePulse(pulseElements.client, pulseElements.commutator, () => {
        // Импульс к коммутатору
        animatePulse(pulseElements.commutator, pulseElements.mds2, () => {
            // Импульсы к MDS серверов 2 и 3
            animatePulse(pulseElements.commutator, pulseElements.mds3, () => {
                    // Обратные импульсы
                animatePulse(pulseElements.mds2, pulseElements.commutator, () => {
                    animatePulse(pulseElements.mds3, pulseElements.commutator, () => {
                        animatePulse(pulseElements.commutator, pulseElements.client, startMainAnimation);
                    });
            });
        });
    });
});
        
        // Параллельно импульс к MDS сервера 1 (без коммутатора)
       animatePulse(pulseElements.client, pulseElements.mds1, () => {
    animatePulse(pulseElements.mds1, pulseElements.client, () => {});
});
    function startMainAnimation() {
    currentAnimationId++;  // Увеличиваем ID при каждом новом запуске
    const animationId = currentAnimationId;  // Фиксируем ID для текущей анимации
    resetAllCubes();
    const config = animationConfig[mode];
    let iteration = 0;
    currentMode = mode;

    const runSequence = () => {
      // Проверяем, нужно ли продолжать анимацию
        if (currentAnimationId !== animationId || currentMode !== mode) {
            return;  // Прерываем если анимация устарела
        }
        if (iteration >= 3) {
            animationTimeout = setTimeout(() => {
                clearStorage();
                if (currentMode === mode) startAnimation(mode);
            }, 1000);
            return;
        }

        const { targets, storage, colors } = config.sequences[iteration];

        targets.forEach((cubeIdx, i) => {
            const storageId = storage[i];
            const originalCube = cubes[cubeIdx];
            
            // Создаем новый кубик
            const newCube = document.createElement('div');
            newCube.className = `cube cube-${cubeIdx+1}`;
            newCube.style.backgroundColor = colors ? colors[cubeIdx] : config.colors[cubeIdx];
            newCube.style.position = 'absolute';
            newCube.style.width = '12px';
            newCube.style.height = '12px';
            newCube.style.borderRadius = '2px';
            newCube.style.opacity = '0';

            // Позиционируем в начальной точке (как оригинальный кубик)
            const rect = originalCube.getBoundingClientRect();
            const containerRect = document.getElementById('animationContainer').getBoundingClientRect();
            newCube.style.left = (rect.left - containerRect.left) + 'px';
            newCube.style.top = (rect.top - containerRect.top) + 'px';

            // Добавляем в DOM сразу (до анимации)
            document.getElementById('animationContainer').appendChild(newCube);

            // Смещение для нового кубика
            const cubeCount = accumulatedCubes[storageId].length;
            const offsetY = -cubeCount * 15; // Большее смещение для лучшей видимости

            // Анимация
            const animation = newCube.animate([
                { transform: 'translate(0, 0)', opacity: 1 },
                { transform: `translate(${storageId === 1 ? 0 : 180}px, ${storageId === 1 ? 100 : -140}px)`, opacity: 1 },
                { transform: `translate(${storageId === 3 ? 385 : storageId === 2 ? 180 : 0}px, ${182 + offsetY}px)`, opacity: 1 }
            ], {
                duration: 2000,
                fill: 'forwards'
            });

            // Сохраняем кубик
            accumulatedCubes[storageId].push(newCube);
        });

        iteration++;
        setTimeout(runSequence, 2000);
    };

    runSequence();
}
}

//Функция очистки хранилищ
    function clearStorage() {
      [1, 2, 3].forEach(storageId => {
        accumulatedCubes[storageId].forEach(cube => cube.remove());
        accumulatedCubes[storageId] = [];
      });
    }

// Улучшенная функция resetAllCubes
function resetAllCubes() {
    clearTimeout(animationTimeout);
    currentMode = null;
    // Очищаем все накопленные кубики
    clearStorage();
    
    // Сбрасываем основные кубики
    cubes.forEach(cube => {
        cube.style.opacity = '0.5';
        cube.style.transform = 'translate(0, 0)';
        cube.style.animation = 'none';
    });
}

// Функция updateButtonStates остается без изменений
function updateButtonStates() {
    document.querySelectorAll('.toggle-button').forEach(btn => {
        const isActive = 
            (toggle2.checked && btn.htmlFor === 'animationToggle2') ||
            (toggle3.checked && btn.htmlFor === 'animationToggle3') ||
            (toggle3Alt.checked && btn.htmlFor === 'animationToggle3Alt');
        
        btn.classList.toggle('active', isActive);
    });
}
</script>
    



