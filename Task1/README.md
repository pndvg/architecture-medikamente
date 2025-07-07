# Задание 1. Анализ безопасности системы

Чтобы заниматься реализацией новых продуктов, которые основаны на данных, необходимо системно решить проблему хранения конфиденциальных данных и управления ими. Сейчас данные лежат в непосредственной близости к пользователям, поэтому существует риск утечки данных и неправомерного использования информации.
Чтобы защитить данные, используйте механизмы, инструменты и принципы работы с конфиденциальными данными, например, подходы Privacy By Design, Data Minimization и Data Lineage.
В рамках этого задания вам нужно проанализировать состояние As-Is, чтобы впоследствии приступить к его доработке.

## Что нужно сделать

1. Выявите конфиденциальные данные, которые не учтены во внутренних системах.

   - Проанализируйте информацию о компании.
   - Создайте диаграммы потоков данных (Data Flow Diagrams). Для каждого процесса создайте отдельную диаграмму.
   - Отобразите на них, как данные перемещаются по системам компании и какие операции над ними совершают.

2. Проведите аудит мер по обеспечению безопасности данных.

   - Сопоставьте процессы в компании с требованиями по обеспечению безопасности данных и архитектурными практиками в области безопасности конфиденциальных данных.
   - Составьте список проблемных зон и сохраните его в отдельный документ.

3. Подумайте, что можно улучшить.

   - Составьте список данных для защиты и проставьте для каждого способы защиты — шифрование, обфускация, обезличивание.
   - Разработайте механизм тегирования данных с использованием инструментов тегирования.
   - Составьте список инструментов, способов и мер, которые позволят обеспечить конфиденциальность данных в указанных потоках.
   - Доработайте диаграммы из предыдущего шага: отобразите на них, что следует использовать на каждом этапе потока.

При работе над диаграммами потоков данных ориентируйтесь на рекомендации, которые мы дали в уроке «Как управлять потоками данных», и гайд от Lucidchard.
Когда задание будет готово, загрузите диаграммы потоков данных и список проблем в директорию Task1 в рамках пул-реквеста.

# Решение. Анализ безопасности системы "Медикаменте"

## 1. Выявление конфиденциальных данных

### Анализ текущего состояния компании

Компания "Медикаменте" обрабатывает следующие категории конфиденциальных данных:

#### Персональные данные пациентов (PII)

- Базовые персональные данные: ФИО, дата рождения, телефон, электронная почта
- Расширенные персональные данные: адрес прописки, место работы/учебы
- Медицинские данные: информация о хронических заболеваниях, результаты анализов, медицинские заключения
- Договорная информация: клиентские контракты, условия обслуживания

#### Финансовые данные

- Платежная информация: данные о платежах пациентов
- Бухгалтерские данные: налоговая отчетность
- Кадровые данные: информация о зарплатах сотрудников

#### Операционные данные

- Данные о сотрудниках: личные данные, должностные обязанности
- Складские данные: информация о ТМЦ, поставщиках
- Системные данные: логи доступа, конфигурации систем

### Проблемы текущего состояния

1. **Отсутствие централизованного управления данными**
   - Данные разбросаны по разным системам (Excel, 1C, файловые хранилища)
   - Нет единой системы классификации и тегирования данных

2. **Недостаточная защита данных при хранении**
   - Файлы Excel и сканы документов хранятся на общем диске без шифрования
   - Отсутствует контроль доступа к конфиденциальным данным

3. **Отсутствие аудита и мониторинга**
   - Нет логирования доступа к данным
   - Отсутствует система отслеживания изменений в данных

4. **Слабая защита при передаче данных**
   - Внутренние потоки данных не контролируются
   - Отсутствует шифрование при передаче данных между системами

### Диаграммы потоков данных (DFD)

#### Процесс 1: Запись пациента на прием

```mermaid
flowchart TD
    %% Внешние сущности
    Patient[👤 Пациент]
    Doctor[👨‍⚕️ Врач]

    %% Процессы
    P1((1.1 Прием заявки))
    P2((1.2 Создание записи))
    P3((1.3 Создание договора))

    %% Хранилища данных
    D1[("D1: Журнал записей(Excel файл)")]
    D2[("D2: Договоры(Excel/сканы)")]
    D3[("D3: Файловоехранилище")]

    %% Потоки данных (As-Is)
    Patient -->|"Заявка на запись(ФИО, телефон, услуга) Незащищенно"| P1
    P1 -->|"Данные записи(время, врач, услуга) Открытый текст"| P2
    P2 -->|"Информация о записи Без шифрования"| D1
    P2 -->|"Уведомление о записи Email/телефон/календарь"| Doctor
    P1 -->|"Персональные данные (ФИО, паспорт, адрес) Открытый текст"| P3
    P3 -->|"Договорные данные Без ЭЦП"| D2
    D1 -->|"Файлы записей Общий доступ"| D3
    D2 -->|"Файлы договоров Общий доступ"| D3

    %% Стили для проблемных элементов
    classDef unsecureFlow fill:#ffebee,stroke:#f44336,stroke-width:2px,color:#d32f2f
    classDef unsecureData fill:#fff3e0,stroke:#ff9800,stroke-width:2px,color:#e65100
    classDef process fill:#e3f2fd,stroke:#2196f3,stroke-width:2px,color:#0d47a1
    classDef entity fill:#e8f5e8,stroke:#4caf50,stroke-width:2px,color:#2e7d32

    class Patient,Doctor entity
    class P1,P2,P3 process
    class D1,D2,D3 unsecureData
```

**Конфиденциальные данные в потоке:**
- ФИО пациента
- Контактная информация
- Медицинские показания
- Договорная информация

**Проблемы безопасности:**
- Данные хранятся в незашифрованном виде
- Отсутствует контроль доступа
- Нет аудита изменений

#### Процесс 2: Проведение медицинского осмотра и ведение медицинской карты

```mermaid
flowchart TD
    %% Внешние сущности
    Patient[👤 Пациент]
    Doctor[👨‍⚕️ Врач]
    Lab[🧪 Лаборатория]

    %% Процессы
    P1((2.1 Проведение осмотра))
    P2((2.2 Ведение мед. карты))
    P3((2.3 Направление на анализы))
    P4((2.4 Получение результатов))

    %% Хранилища данных
    D1[("🏥 D1: Медицинские карты (Excel/сканы)")]
    D2[("📋 D2: Направления на анализы")]
    D3[("🧾 D3: Результаты анализов")]
    D4[("D4: Файловое хранилище")]

    %% Потоки данных (As-Is)
    Patient -->|"Жалобы, симптомы Устная передача"| Doctor
    Doctor -->|"Жалобы, симптомы Устная передача"| P1
    Patient -->|"Участие в осмотре"| P1
    Patient -->|"Анализы"| P3
    P1 -->|"Данные осмотра Незащищенные записи"| P2
    P2 -->|"Медицинские данные Открытый текст"| D1
    P1 -->|"Показания для анализов Бумажное направление"| P3
    P3 -->|"Направление Незащищенная передача"| D2
    P3 -->|"Запрос анализов Факс/email"| Lab
    Lab -->|"Результаты анализов Незащищенная передача"| P4
    P4 -->|"Результаты Без подтверждения целостности"| D3
    D1 -->|"Архивирование Общий доступ"| D4
    D2 -->|"Архивирование Общий доступ"| D4
    D3 -->|"Архивирование Общий доступ"| D4

    %% Стили
    classDef unsecureFlow fill:#ffebee,stroke:#f44336,stroke-width:2px,color:#d32f2f
    classDef unsecureData fill:#fff3e0,stroke:#ff9800,stroke-width:2px,color:#e65100
    classDef process fill:#e3f2fd,stroke:#2196f3,stroke-width:2px,color:#0d47a1
    classDef entity fill:#e8f5e8,stroke:#4caf50,stroke-width:2px,color:#2e7d32
    classDef external fill:#f3e5f5,stroke:#9c27b0,stroke-width:2px,color:#6a1b9a
    classDef issues fill:#ffebee,stroke:#f44336,stroke-width:2px,color:#d32f2f

    class Patient,Doctor entity
    class Lab external
    class P1,P2,P3,P4 process
    class D1,D2,D3,D4 unsecureData
    class Issue1,Issue2,Issue3,Issue4,Issue5 issues
```

**Конфиденциальные данные в потоке:**

- Медицинские данные пациента
- Результаты обследований
- Диагнозы и назначения
- Результаты анализов

**Проблемы безопасности:**

- Медицинские данные хранятся в открытом виде
- Отсутствует шифрование при передаче в лабораторию
- Нет контроля доступа к медицинским картам

#### Процесс 3: Обработка платежей

```mermaid
flowchart TD
    %% Внешние сущности
    Patient[👤 Пациент]
    Accountant[👩‍Бухгалтерия]

    %% Процессы
    P1((3.1 Прием платежа))
    P2((3.2 Двойной учет))
    P3((3.3 Обработка в 1С))

    %% Системы
    KKM[💳 ККМ]
    OneC[1С:Бухгалтерия]

    %% Хранилища данных
    D1[("D1: Платежи Excel")]
    D2[("D2: База 1С")]
    D3[("🧾 D3: Фискальные документы")]

    %% Потоки данных (As-Is)
    Patient -->|"Оплата услуги (сумма, способ)  Карта/наличные"| P1
    P1 -->|"Данные платежа (сумма, пациент)  Открытый текст"| P2
    P2 -->|"Запись в Excel  Дублирование данных"| D1
    P1 -->|"Чек, документы  Бумажные документы"| Patient
    P2 -->|"Фискальные данные  OLE интеграция"| KKM
    P2 -->|"Учетные данные  Файловый режим"| P3
    P3 -->|"Бухгалтерские проводки  Локальная база"| OneC
    D1 -->|"Дополнение данных в 1C"| P3
    OneC -->|"База данных  Файловое хранение"| D2
    KKM -->|"Фискальные отчеты  Локальное хранение"| D3
    D2 -->|"Отчеты для налоговой  Незащищенная передача"| Accountant

    %% Интеграция между системами
    OneC -.->|"TCP/IP + OLE  Незащищенный канал"| KKM

    %% Стили
    classDef unsecureFlow fill:#ffebee,stroke:#f44336,stroke-width:2px,color:#d32f2f
    classDef unsecureData fill:#fff3e0,stroke:#ff9800,stroke-width:2px,color:#e65100
    classDef process fill:#e3f2fd,stroke:#2196f3,stroke-width:2px,color:#0d47a1
    classDef entity fill:#e8f5e8,stroke:#4caf50,stroke-width:2px,color:#2e7d32
    classDef system fill:#efebe9,stroke:#795548,stroke-width:2px,color:#3e2723
    classDef issues fill:#ffebee,stroke:#f44336,stroke-width:2px,color:#d32f2f

    class Patient,Accountant entity
    class P1,P2,P3 process
    class KKM,OneC system
    class D1,D2,D3 unsecureData
    class Issue1,Issue2,Issue3,Issue4,Issue5 issues
```

**Конфиденциальные данные в потоке:**

- Платежная информация
- Персональные данные плательщика
- Финансовые операции

**Проблемы безопасности:**

- Дублирование данных в разных системах
- Отсутствует шифрование платежных данных
- Нет централизованного аудита финансовых операций

#### Процесс 4: Управление складом и ТМЦ

```mermaid
flowchart TD
    %% Внешние сущности
    Supplier[🏢 Поставщик]
    WarehouseStaff[👷 Сотрудник склада]

    %% Процессы
    P1((4.1 Прием товара))
    P2((4.2 Учет в 1С))

    %% Системы
    OneCTrade[📦 1С:Торговля и склад]
    OneCAccount[1С:Бухгалтерия]

    %% Хранилища данных
    D1[("📋 D1: Накладные поставщиков")]
    D2[("D2: Складские остатки")]

    %% Потоки данных (As-Is)
    Supplier -->|"Поставка товара (накладная, товар)  Бумажные документы"| P1
    P1 -->|"Данные о поступлении  Ручной ввод"| P2
    P2 -->|"Остатки ТМЦ  Файловый режим"| OneCTrade
    OneCTrade -->|"Складские данные  Локальная база"| D2
    P1 -->|"Накладные  Сканы документов"| D1
    WarehouseStaff -->|"Передает данные о заведении товара на склад"| P1


    %% Интеграция между системами
    OneCTrade -.->|"Интеграция данных  Файловый обмен"| OneCAccount

    %% Коммерческие данные
    subgraph CommercialData["Коммерческие данные"]
        CD1["Договоры с поставщиками"]
        CD2["Цены и условия поставок"]
        CD3["Объемы закупок"]
        CD4["Финансовые условия"]
    end

    %% Связи с коммерческими данными
    Supplier -.->|"Содержат"| CommercialData
    D1 -.->|"Хранят"| CommercialData
    OneCTrade -.->|"Обрабатывают"| CommercialData

    %% Стили
    classDef unsecureFlow fill:#ffebee,stroke:#f44336,stroke-width:2px,color:#d32f2f
    classDef unsecureData fill:#fff3e0,stroke:#ff9800,stroke-width:2px,color:#e65100
    classDef process fill:#e3f2fd,stroke:#2196f3,stroke-width:2px,color:#0d47a1
    classDef entity fill:#e8f5e8,stroke:#4caf50,stroke-width:2px,color:#2e7d32
    classDef system fill:#efebe9,stroke:#795548,stroke-width:2px,color:#3e2723
    classDef commercial fill:#e8eaf6,stroke:#3f51b5,stroke-width:2px,color:#1a237e
    classDef issues fill:#ffebee,stroke:#f44336,stroke-width:2px,color:#d32f2f

    class Supplier,WarehouseStaff entity
    class P1,P2,P3 process
    class OneCTrade,OneCAccount system
    class D1,D2,D3 unsecureData
    class CD1,CD2,CD3,CD4 commercial
    class Issue1,Issue2,Issue3,Issue4,Issue5 issues
```

**Конфиденциальные данные в потоке:**

- Договорная информация с поставщиками
- Финансовые данные о закупках
- Данные о медицинском оборудовании

**Проблемы безопасности:**

- Отсутствует защита коммерческой информации
- Нет контроля доступа к данным о поставщиках

## 2. Аудит мер по обеспечению безопасности данных

### Соответствие требованиям российского законодательства

#### Федеральный закон "О персональных данных" (152-ФЗ)

- Отсутствует согласие на обработку персональных данных
- Нет уведомления Роскомнадзора об обработке ПДн
- Отсутствуют технические меры защиты ПДн
- Нет назначенного ответственного за обработку ПДн

#### Федеральный закон "Об охране здоровья граждан" (323-ФЗ)

- Медицинская тайна не защищена должным образом
- Отсутствует контроль доступа к медицинским данным
- Нет системы аудита доступа к медицинской информации

#### Требования по информационной безопасности

- Отсутствует классификация информации по уровням конфиденциальности
- Нет политики информационной безопасности
- Отсутствует система управления инцидентами ИБ

### Архитектурные практики безопасности

#### Privacy by Design

- Безопасность не заложена в основу архитектуры
- Отсутствует принцип минимизации данных
- Нет встроенной приватности по умолчанию

#### Data Minimization

- Собираются избыточные данные
- Отсутствует политика удаления данных
- Нет ограничений на время хранения данных

#### Data Lineage

- Отсутствует отслеживание происхождения данных
- Нет визуализации потоков данных
- Отсутствует документирование трансформаций данных

### Список проблемных зон

### Критические проблемы

1. Отсутствие шифрования данных - данные хранятся в открытом виде
2. Слабый контроль доступа - любой сотрудник может получить доступ к любым данным
3. Отсутствие аудита - нет логирования доступа к конфиденциальным данным
4. Несоответствие 152-ФЗ - высокий риск штрафов и репутационных потерь

### Высокие риски

1. Утечка медицинских данных - нарушение врачебной тайны
2. Компрометация платежных данных - финансовые риски
3. Нарушение целостности данных - возможность несанкционированного изменения
4. Отсутствие резервного копирования - риск потери данных

### Средние риски

1. Неэффективное управление данными - избыточное дублирование
2. Слабая интеграция систем - риск рассинхронизации данных
3. Отсутствие мониторинга - невозможность выявления инцидентов

## 3. Рекомендации по улучшению

### Классификация данных для защиты

| Тип данных | Уровень конфиденциальности | Способ защиты |
|------------|---------------------------|---------------|
| Медицинские данные | Критический | Шифрование AES-256, обфускация |
| Персональные данные | Высокий | Шифрование, псевдонимизация |
| Платежная информация | Высокий | Шифрование, токенизация |
| Договорная информация | Средний | Шифрование, контроль доступа |
| Операционные данные | Низкий | Контроль доступа, аудит |

### Механизм тегирования данных

#### Предлагаемые теги

- **Sensitivity**: PUBLIC, INTERNAL, CONFIDENTIAL, RESTRICTED
- **DataType**: MEDICAL, PERSONAL, FINANCIAL, OPERATIONAL
- **Retention**: 1Y, 3Y, 7Y, PERMANENT
- **Access**: DOCTOR, ADMIN, RECEPTION, CASHIER
- **Compliance**: GDPR, 152FZ, MEDICAL_SECRET

#### Инструменты тегирования

- **Apache Atlas** - для управления метаданными (вероятно лучшее для РФ)
- **Собственное решение** - на базе аннотаций в базе данных

### Инструменты и меры защиты

#### Шифрование данных

- **At Rest**: AES-256 для файлов, TDE для БД
- **In Transit**: TLS 1.3 для сетевого трафика
- **In Use**: Homomorphic encryption для аналитики

#### Контроль доступа

- **RBAC** - ролевое управление доступом
- **ABAC** - атрибутное управление доступом
- **MFA** - многофакторная аутентификация

#### Мониторинг и аудит

- **SIEM** - система управления событиями безопасности
- **DLP** - предотвращение утечек данных
- **Database Activity Monitoring** - мониторинг активности БД

#### Управление ключами

- **Key Management System** - централизованное управление ключами
- **Hardware Security Module** - аппаратная защита ключей

### Архитектурные изменения

#### Новые компоненты архитектуры

- **Data Governance Platform** - платформа управления данными
- **Privacy Management System** - система управления приватностью
- **Audit and Compliance System** - система аудита и соответствия
- **Identity and Access Management** - система управления идентификацией
- **Data Loss Prevention** - система предотвращения утечек

### Обновленные диаграммы

#### Запись пациента с мерами защиты

```mermaid
flowchart TB
    %% Внешние сущности
    Patient[👤 Пациент]
    Doctor[👨‍⚕️ Врач]

    %% Процессы с защитой
    P1((1.1 Аутентификация и прием заявки))
    P2((1.2 Создание записи с тегированием))
    P3((1.3 Создание договора с ЭЦП))

    %% Компоненты безопасности
    IAM[IAM System MFA + RBAC]
    DataClass[🏷️ Data Classification & Tagging Engine]
    PKI[🔑 PKI System Digital Signatures]
    AuditSys[Audit & Monitoring System]

    %% Защищенные хранилища данных
    D1[("D1: Зашифрованная база Журнал записей + теги")]
    D2[("D2: Безопасное хранилище: Договоры с ЭЦП")]
    D3[("D3: SIEM Logs Аудит действий")]

    %% Защищенные потоки данных
    Patient -->|"Заявка + биометрия [TAG: CONFIDENTIAL] TLS 1.3"| IAM
    IAM -->|"Авторизованный запрос [VERIFIED]"| P1
    P1 -->|"Данные записи [TAG: MEDICAL_SECRET] зашифрованно"| DataClass
    DataClass -->|"🏷️ Тегированные данные [MEDICAL, CONFIDENTIAL, 7Y]"| P2
    P2 -->|"Зашифрованные данные AES-256"| D1
    P2 -->|"Уведомление End-to-end шифрование"| Doctor

    P1 -->|"ПДн с согласием [TAG: PERSONAL_DATA] HTTPS"| P3
    P3 -->|"📝 Договор с ЭЦП [SIGNED, IMMUTABLE]"| PKI
    PKI -.->|"Legal compliance"| Compliance
    PKI -->|"Signed document Digital signature"| D2

    %% Аудит всех операций
    P1 -.->|"Log: Authentication"| AuditSys
    P2 -.->|"Log: Data access"| AuditSys
    P3 -.->|"Log: Contract creation"| AuditSys
    AuditSys -->|"📋 Audit trail Tamper-proof logs"| D3

    %% Автоматические процессы защиты
    subgraph AutoProtection["🛡️ Автоматическая защита"]
        DataRetention[⏰ Data Retention Policy Автоудаление по тегам]
        AccessControl[🚪 Dynamic Access Control По ролям и атрибутам]
        Compliance[Compliance Checker 152-ФЗ валидация]
    end

    D1 -.->|"🏷️ Based on tags"| DataRetention
    DataClass -.->|"Policy enforcement"| AccessControl

    %% Теги и классификация
    subgraph TagSystem["🏷️ Система тегирования"]
        direction LR
        SensTag["SENSITIVITY: CONFIDENTIAL"]
        TypeTag["DATA_TYPE: MEDICAL"]
        RetTag["RETENTION: 7Y"]
        AccessTag["ACCESS: DOCTOR,RECEPTION"]
        CompTag["COMPLIANCE: 152FZ"]
    end

    DataClass -.->|"Автоматическое тегирование"| TagSystem

    %% Стили
    classDef secureFlow fill:#e8f5e8,stroke:#4caf50,stroke-width:3px,color:#2e7d32
    classDef secureData fill:#e3f2fd,stroke:#2196f3,stroke-width:3px,color:#0d47a1
    classDef process fill:#e3f2fd,stroke:#2196f3,stroke-width:2px,color:#0d47a1
    classDef entity fill:#e8f5e8,stroke:#4caf50,stroke-width:2px,color:#2e7d32
    classDef security fill:#fce4ec,stroke:#e91e63,stroke-width:2px,color:#ad1457
    classDef protection fill:#f3e5f5,stroke:#9c27b0,stroke-width:2px,color:#6a1b9a
    classDef tags fill:#fff3e0,stroke:#ff9800,stroke-width:1px,color:#e65100

    class Patient,Doctor entity
    class P1,P2,P3 process
    class D1,D2,D3 secureData
    class IAM,DataClass,AuditSys,PKI security
    class DataRetention,AccessControl,Compliance protection
    class SensTag,TypeTag,RetTag,AccessTag,CompTag tags
```

#### Медицинский осмотр с защитой тайны пациента

```mermaid
flowchart TD
    %% Внешние сущности
    Patient[👤 Пациент]
    Doctor[👨‍⚕️ Врач]
    Lab[🧪 Лаборатория]

    %% Процессы с защитой
    P1((2.1 Осмотр с контролем доступа))
    P2((2.2 Ведение защищенной мед. карты))
    P3((2.3 Безопасная передача в лабораторию))
    P4((2.4 Получение результатов))

    %% Компоненты безопасности медицинских данных
    MedProtect[🏥 Medical Data Protection System]
    Anonymizer[🎭 Data Anonymization & Pseudonymization]
    APIGateway[🚪 API Gateway with Authentication]
    HIESystem[🔗 Health Information Exchange HIE]

    %% Защищенные хранилища
    D1[("D1: Encrypted Medical DB Мед. карты + теги конфиденциальности")]
    D2[("D2: Secure Lab Exchange Анализы с обфускацией")]
    D3[("📋 D3: Medical Audit Logs Аудит доступа к мед. тайне")]

    %% Защищенные потоки данных
    Patient -->|"Жалобы, симптомы [TAG: MEDICAL_SECRET] E2E encrypted"| P1
    Doctor -->|"Врач аутентифицирован [ROLE: ATTENDING_DOCTOR]"| MedProtect
    MedProtect -->|"Access granted Medical secret access"| P1

    P1 -->|"Данные осмотра [ENCRYPTED, TAGGED] AES-256"| P2
    P2 -->|"Медицинские данные [MEDICAL_SECRET, 25Y] Encrypted at rest"| D1

    P1 -->|"Обезличенные показания [ANONYMIZED] Pseudonymized"| Anonymizer
    Anonymizer -->|"Анонимные данные Токенизированные"| P3
    P3 -->|"FHIR + TLS 1.3 [SECURE_TRANSPORT]"| APIGateway
    APIGateway -->|"Authenticated request mTLS"| Lab

    Lab -->|"Результаты анализов [ENCRYPTED_RESULTS] Digital signature"| HIESystem
    HIESystem -->|"Validated results Integrity verified"| P4
    P4 -->|"Результаты с подписью и ссылкой на запись в лаборатории [VERIFIED, IMMUTABLE]"| D1
    P4 -->|"Результаты с подписью [VERIFIED, IMMUTABLE]"| D2

    %% Специальная защита медицинской тайны
    subgraph MedicalSecrecy["🏥 Защита медицинской тайны"]
        direction TB
        ConsentMgmt[📋 Consent Management Управление согласиями]
        AccessLog[📋 Medical Access Log Кто, когда, зачем]
        DataMinimize[🎯 Data Minimization Минимум данных для цели]
        AutoDelete[⏰ Automatic Deletion По срокам хранения]
    end

    P1 -.->|"📋 Check consent"| ConsentMgmt
    MedProtect -.->|"Log medical access"| AccessLog
    Anonymizer -.->|"🎯 Minimize data"| DataMinimize
    D1 -.->|"⏰ Auto-delete old records"| AutoDelete

    AccessLog -->|"Encrypted audit trail"| D3

    %% Контроль доступа к медицинской информации
    subgraph AccessMatrix["Матрица доступа к медданным"]
        direction LR
        DoctorAccess["👨‍⚕️ Лечащий врач: FULL"]
        PatientAccess["👤 Пациент: OWN_DATA"]
        AdminAccess["👨‍Администратор: METADATA"]
        LabAccess["🧪 Лаборатория: ANONYMIZED"]
    end

    MedProtect -.->|"Контроль доступа"| AccessMatrix

    %% Стили
    classDef secureFlow fill:#e8f5e8,stroke:#4caf50,stroke-width:3px,color:#2e7d32
    classDef secureData fill:#e3f2fd,stroke:#2196f3,stroke-width:3px,color:#0d47a1
    classDef process fill:#e3f2fd,stroke:#2196f3,stroke-width:2px,color:#0d47a1
    classDef entity fill:#e8f5e8,stroke:#4caf50,stroke-width:2px,color:#2e7d32
    classDef external fill:#f3e5f5,stroke:#9c27b0,stroke-width:2px,color:#6a1b9a
    classDef security fill:#fce4ec,stroke:#e91e63,stroke-width:2px,color:#ad1457
    classDef medical fill:#e8f5e8,stroke:#4caf50,stroke-width:2px,color:#2e7d32
    classDef access fill:#fff3e0,stroke:#ff9800,stroke-width:1px,color:#e65100
    classDef encryption fill:#f3e5f5,stroke:#9c27b0,stroke-width:1px,color:#6a1b9a

    class Patient,Doctor entity
    class Lab external
    class P1,P2,P3,P4 process
    class D1,D2,D3 secureData
```

### Обработка платежей с защитой

```mermaid
flowchart TD
    %% Внешние сущности
    Patient[👤 Пациент]
    Accountant[👩‍Бухгалтерия]
    Bank[🏦 Банк]
    TaxService[🏛️ Налоговая служба]

    %% Процессы с защитой
    P1((3.1 Безопасный прием платежа))
    P2((3.2 Централизованный учет))
    P3((3.3 Защищенная обработка в системе))
    P4((3.3 Подготовка отчетности в системе))

    %% Компоненты финансовой безопасности
    PaymentGateway[💳 Платежный Gateway]
    Tokenizer[🎫 Токенизатор платежей]
    FinAudit[Система финансового аудита и комплайенса]
    Reconciliation[⚖️ Auto Reconciliation System]

    %% Защищенные системы
    SecureKKM[Secure ККМ Encrypted fiscal data]
    ProtectedERP[🛡️ Protected ERP 1С with encryption]

    %% Защищенные хранилища
    D1[("D1: Encrypted Payment DB Токенизированные платежи")]
    D2[("D2: 1C DB Зашифрованные проводки")]
    D3[("📋 D3: Логи аудита финансовых операций")]


    %% Защищенные потоки данных
    Patient -->|"💳 Платежные данные [TOKENIZED]"| PaymentGateway
    PaymentGateway -->|"Validated payment [FRAUD_CHECKED]"| P1
    P1 -->|"🎫 Токенизированные платежные данные [TOKENIZED, FINANCIAL]"| Tokenizer
    Tokenizer -->|"Secure tokens Original data in HSM"| P2
    Accountant <-->|"Контроль и управление проводками и отчетностью"| P4
    FinAudit -->|"Контроль и управление проводками и отчетностью"| P4
    FinAudit-->|"Фиксация проведенных операций"|D3


    P2 -->|"Шифрованные финансовые данные [AES-256]"| D1
    P2 -->|"🧾 Fiscal data [ENCRYPTED, SIGNED]"| SecureKKM
    SecureKKM -->|"Secure fiscal transmission mTLS + digital signature"| P3
    P3 -->|"Protected accounting entries [ENCRYPTED_AT_REST]"| ProtectedERP
    ProtectedERP -->|"Encrypted database Column-level encryption"| D2

    %% Банковская интеграция
    P3 <-->|"Secure bank integration End-to-end encrypted"| Bank

    Bank <-.->|"Bank statements [ENCRYPTED, SIGNED]"| Reconciliation
    Reconciliation -->|"⚖️ Reconciliation data [VALIDATED]"| D2

    %% Налоговая отчетность
    P4 -->|"Encrypted tax reports [COMPLIANCE_READY]"| FinAudit
    FinAudit -->|"🏛️ Налоговые данные,, подписанные электронной подписью"| TaxService

    %% Финансовый аудит и мониторинг
    subgraph FinancialSecurity["Финансовая безопасность"]
        RealTimeMonitor[Real-time Monitoring на подозрительные операции]
        ComplianceCheck[Соответствие требованиям 152-ФЗ, ПОД/ФТ]
        DataIntegrity[Data Integrity Контроль целостности]
        BackupSystem[Secure Backup Зашифрованные копии]
    end

    P1 -.->|"Мониторинг операций"| RealTimeMonitor
    D3 -.->|"Мониторинг транзакций"| RealTimeMonitor
    P2 -.->|"Check compliance"| ComplianceCheck
    D1 -.->|"Verify integrity"| DataIntegrity
    D2 -.->|"Secure backup"| BackupSystem

    %% Логирование финансовых операций
    P1 -.->|"📋 Log: Payment received"| FinAudit
    P2 -.->|"📋 Log: Data processing"| FinAudit
    P3 -.->|"📋 Log: ERP integration"| FinAudit
    FinAudit <-->|"Подготовленная отчетность | Данные о платежах"| D2

    PaymentGateway -.->|"Соответствие стандарту"| PCICompliance

    %% Стили
    classDef secureFlow fill:#e8f5e8,stroke:#4caf50,stroke-width:3px,color:#2e7d32
    classDef secureData fill:#e3f2fd,stroke:#2196f3,stroke-width:3px,color:#0d47a1
    classDef process fill:#e3f2fd,stroke:#2196f3,stroke-width:2px,color:#0d47a1
    classDef entity fill:#e8f5e8,stroke:#4caf50,stroke-width:2px,color:#2e7d32
    classDef external fill:#f3e5f5,stroke:#9c27b0,stroke-width:2px,color:#6a1b9a
    classDef security fill:#fce4ec,stroke:#e91e63,stroke-width:2px,color:#ad1457
    classDef financial fill:#fff3e0,stroke:#ff9800,stroke-width:2px,color:#e65100
    classDef system fill:#efebe9,stroke:#795548,stroke-width:2px,color:#3e2723
    classDef pci fill:#e8eaf6,stroke:#3f51b5,stroke-width:1px,color:#1a237e
    classDef encryption fill:#f3e5f5,stroke:#9c27b0,stroke-width:1px,color:#6a1b9a

    class Patient,Accountant entity
    class Bank,TaxService external
    class P1,P2,P3,P4 process
    class D1,D2,D3,D4 secureData
    class PaymentGateway,Tokenizer,FraudDetect,FinAudit,Reconciliation security
    class SecureKKM,ProtectedERP system
    class RealTimeMonitor,ComplianceCheck,DataIntegrity,BackupSystem financial
    class NetworkSeg,AccessControl,VulnMgmt,SecureDev pci
    class CardData,TransData,KeyMgmt,TokenVault encryption
```

## Заключение

Текущее состояние системы безопасности в компании "Медикаменте" не соответствует требованиям российского законодательства и современным стандартам защиты конфиденциальных данных. Необходимо комплексное решение проблем безопасности с внедрением современных технологий защиты данных и приведением процессов в соответствие с принципами Privacy by Design.

Приоритетные шаги:

1. Внедрение системы классификации и тегирования данных
2. Реализация шифрования данных при хранении и передаче
3. Внедрение системы контроля доступа и аудита
4. Приведение процессов в соответствие с требованиями 152-ФЗ
5. Обучение персонала основам информационной безопасности
