[Docs](https://lightning.ai/docs/pytorch/stable/starter/installation.html)

[Lightning Universe](https://github.com/Lightning-Universe) (Комьюнити проекты, расширения и доп возможности)

[Lightning Flash](https://github.com/Lightning-Universe/lightning-flash) (Зоопарк готовых моделей)

[Lightning Bolts](https://lightning-bolts.readthedocs.io/en/latest/) (Еще один зоопарк моделей, датасетов и супер-коллбеков)

[Train model with billions of parameters](https://lightning.ai/docs/pytorch/stable/advanced/model_parallel.html)

<details markdown="1">
<summary>Не проверял, возможно мусор</summary>

- [Сахарок для S3](https://github.com/Lightning-Universe/AWS-s3_component)
- [Сахарок для Redis](https://github.com/Lightning-Universe/Redis_component)
- [Lightning GPT](https://github.com/Lightning-Universe/lightning-GPT)

</details>


# Intro
Мир обучения нейронок уже давно "перешагнул" за `train_loop(...)`, `optimizer.step()`, ..., `zero_grad`, ...,
и ровно как вы не дергаете Cuda из питона и не пишите полносвязанные слои руками: ровно так же не стоит писать тренировку модели руками, потому что:
- это долго
- это баговано
- каждый раз одно и то же
- множество вещей вы просто не знаете как написать (и это нормально!)
- поддерживать простыню кода в тясячи сток просто невозможно, если вы не мазохист
- процесс добавления распределенного обучения для вас станет последней ступенью ада

В общем, если вы не получаете удовлетворения от боли и страданий (и даже если да, то все равно), то призываю использовать готовые системы автоматизии обучения.
Кроме того, неоспоримым плюсом любого кода является его читаемость, переиспользуемость и единообразие; это безусловно касается и кода для обучения DL моделей.
Есть еще такая штука как "синдром неприятия чужого кода", вот определение из википедии:

---

_Синдром неприятия чужой разработки (NIH-синдром от англ. not invented here — изобретено не нами) — позиция в социальной, корпоративной или организационной культурах, при которой избегается использование или покупка уже существующих разработок, исследований, стандартов или знаний из-за их внешнего происхождения и затрат._

_Причины для нежелающих использовать труд других разнообразны, среди них — страх перед нарушением патентного права, непонимание чужой работы, нежелание признать или оценить труд других, ревность или как часть более широкой «войны за территорию» (turf war[англ.])[1]. Как социальное явление, эта философия проявляется нежеланием принять идею или продукт, потому что тот происходит из другой культуры, форма трайбализма[2]._

_В программировании также часто ссылаются на NIH-синдром как на тенденцию «изобретать велосипед», основываясь на убеждении, что собственная разработка по своей природе более приспособленная, безопасная, контролируемая, быстрее развивается и требует меньше общих расходов (включая эксплуатационные расходы), чем существующие реализации._

_Основные доводы в пользу подхода NIH:_


- _сторонние компоненты или услуги иногда не оправдывают ожиданий, когда требуется высокое качество[4];_
- _сущность, находящаяся за пределами собственного контроля, будет привязана к поставщику и несёт постоянную угрозу для бизнеса пропорционально последствиям её потери[5];_
- _закрытые решения могут быть восприняты как недостаточно гибкие в будущем._

-------

И до тех пор пока вы не огромная бигтех компания в которой разработкой и использованием своего in-house фреймворка занимаются десятки/сотни человек, нет никакого смысла "выпендриваться" тем, что вас научили на первом курсе ML писать train-loop на чистом pytorch.


# Installation
[Installation Guide](https://lightning.ai/docs/pytorch/stable/starter/installation.html)

```bash
# Pip
python -m pip install lightning

# Conda
conda install lightning -c conda-forge
```

# Concepts
Строго говоря, основная конценция заключается в избавлении разработчика от необходимости писать boilerplate код.

- **Тренировка и Валидация**: Lightning автоматизирует переключение между тренировочным и валидационным режимами (а так же test и predict режимы), контролирует циклы обучения, и предоставляет полезные коллбеки.
- **GPU и TPU**: Lightning обеспечивает простое управление ресурсами, автоматизируя развертывание модели на доступные устройства (GPUs/TPUs).
- **Масштабирование**: с легкостью (реально одной строчкой) можно использовать режимы распределенного обучения (и даже инференса)
    - Встроенные стратегии:
        - [DDP](https://lightning.ai/docs/pytorch/stable/api/lightning.pytorch.strategies.DDPStrategy.html#lightning.pytorch.strategies.DDPStrategy)
        - [DeepSpeed](https://lightning.ai/docs/pytorch/stable/api/lightning.pytorch.strategies.DeepSpeedStrategy.html#lightning.pytorch.strategies.DeepSpeedStrategy)
        - [Fully Sharded DP](https://lightning.ai/docs/pytorch/stable/api/lightning.pytorch.strategies.FSDPStrategy.html#lightning.pytorch.strategies.FSDPStrategy)
    - Интеграция со сторонними:
        - [Horovod](https://github.com/Lightning-Universe/lightning-Horovod)
        - [Hive Mind](https://github.com/Lightning-Universe/lightning-Hivemind)
        - [Fairscale](https://github.com/Lightning-Universe/lightning-Fairscale)
        - [Colossal-AI](https://github.com/Lightning-Universe/lightning-ColossalAI)
        - [Bagua](https://github.com/Lightning-Universe/lightning-Bagua)
- **Модульность и переиспользуемость**:
    - `LightningDataModule`: Объединяет в себе весь верхний уровень работы с данными (под нижним уровнем понимаются Dataset, Collator, ...). Как и модель имеет state_dict, может загружаться в чекпонинт
    - `LightningModule`: Объединяет в себе модель, оптимизатор и логику тренировочного цикла. Может быть использован для предсказаний, а также сохранен и загружен без проблем.
    - `Callbacks`: Позволяют встраивать дополнительную логику в тренировочный цикл без изменения исходного кода модели. А так же есть зоопарк коробчных и сторонних колбеков, отвечающих практически за все хуки, которые вам захочется добавить
- **Разделение логики**:
    - Research vs Engineering: Lightning позволяет разработчикам сосредотачиваться на исследовательской части работы, выделяя логику тренировочного цикла и обработку данных в отдельные абстракции.
- **Совместимость и Интеграция**:
    - `LightningModule`: наследуется от torch.nn.Module. Lightning предоставляет естественный переход от стандартного кода PyTorch, позволяя легко адаптировать существующий код.
    - Совместимость с библиотеками: Обширная поддержка различных библиотек и фреймворков для логирования, визуализации и др.
- **Безопасность и Отказоустойчивость**:
    - Остановка и Возобновление: Возможность остановки обучения и последующего возобновления с того же места.
    - Очистка: Автоматическое освобождение ресурсов GPU после тренировки.
- **Продвинутые Техники из коробки**:
    - **N-bit Precision**: Простой переход к половинной точности для увеличения скорости обучения, сокращения затрат на память и сохранения точности модели.
        - `bf16-mixed`
        - **BitSandBytes** ([BNB](https://lightning.ai/docs/pytorch/stable/common/precision_intermediate.html#quantization-via-bitsandbytes)):
            - `nf4`: Нормализованный float 4-bit тип данных
            - `nf4-dq`: "dq" расшифровывается как "Double Quantization", что позволяет уменьшить средний объем занимаемой памяти за счет квантования констант квантования. В среднем это составляет около 0.37 бит на параметр (примерно 3 ГБ для 65B модели)
            - `fp4`: Использует стандартный float 4-bit тип данных
            - `fp4-dq`: "dq" расшифровывается как "Double Quantization", что позволяет уменьшить средний объем занимаемой памяти за счет квантования констант квантования. В среднем это составляет около 0.37 бит на параметр (примерно 3 ГБ для 65B модели).
            - `int8`: Использует unsigned int8 тип данных.
            - `int8-training`: int8 для активаций и fp16 для весов.
    - **Gradient Accumulation / Clipping**: Накопление градиентов за несколько итераций перед выполнением шага оптимизации для эффективного использования ресурсов.
- **On prem clusters и Slurm**: [tutorial](https://lightning.ai/docs/pytorch/stable/clouds/cluster_intermediate_1.html)
- **Community and Contribution**: громадный и быстрорастущий комьюнити


# Loggers
- Логгер (или их список) передается напрямую в `pl.Trainer`
- В логгер можно писать через `self.log()` внутри Lightning модулей
- Так же в логгер пишут дополнительные коллбеки и плагины, которые вы передаете в `pl.Trainer`
- Некоторые из готовых логгеров:
    - [CSV Logger](https://lightning.ai/docs/pytorch/stable/api/lightning.pytorch.loggers.csv_logs.html#module-lightning.pytorch.loggers.csv_logs)
    - [Mlflow Looger](https://lightning.ai/docs/pytorch/stable/api/lightning.pytorch.loggers.mlflow.html#module-lightning.pytorch.loggers.mlflow)
    - [Tensorboard Logger](https://lightning.ai/docs/pytorch/stable/api/lightning.pytorch.loggers.tensorboard.html#module-lightning.pytorch.loggers.tensorboard)
    - [Wandb Logger](https://lightning.ai/docs/pytorch/stable/api/lightning.pytorch.loggers.wandb.html#module-lightning.pytorch.loggers.wandb)


# Callbacks
- Коллебки передатся в `pl.Trainer` и позволяют выполнить какое-то действие в конкретный момент процесса обучения, не изменяя исходный код модели.
- Пользователь может создавать собственные коллбеки, оверрайдя методы базового `Callback` класса.
- Некоторые полезные коллбеки, которые встроены в Lightning:
    - `ModelCheckpoint`: автоматически сохраняет чекпоинты модели в процессе обучения
    - `EarlyStopping`: останавливает обучение, когда заданная метрика перестает улучшаться
    - `LearningRateMonitor`: сохраняет значения скорости обучения в логгер
    - `GradientAccumulationScheduler`: накопление градиентов на протяжении обучения
    - `BackboneFinetuning`: позволяет тонко настраивать заданные слои модели в различных фазах обучения.
    - `StochasticWeightAveraging`: реализует SWA для улучшения устойчивости модели.
    - `QuantizationAwareTraining` (выпилили в последних версиях так как часто багует, но можно копипастнуть): для обучения, учитывая квантование весов, что полезно для деплоя
    - `Pruning`: вы и сами все знаете зачем



# Plugins and Environments
[plugind](https://lightning.ai/docs/pytorch/stable/api_references.html#plugins)

[environments](https://lightning.ai/docs/pytorch/stable/api_references.html#environments)
