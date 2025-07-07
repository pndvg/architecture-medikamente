# Задание 2. Проектирование решения

Используя подход Privacy by Design, проведите анализ и предложите механизмы для предупреждения возможных рисков, связанных с конфиденциальностью. Ваша задача — спроектировать механизмы управления такими рисками до момента реализации изменений в проектируемой системе.
В рамках этого задания вам нужно проанализировать состояние As-Is и оценить, как спроектировать решение с учётом требований To-Be.

## Что нужно сделать

1. Предложить новые блоки в архитектуру проектируемой системе С4, которые обеспечат соблюдение принципов Privacy By Design во многих блоках реализуемой системы.
2. Предложить в целевой архитектуре слой, который обеспечивает аналитическую работу с данными системы с учётом принципов Privacy by Design.

Когда задание будет готово, загрузите диаграмму контекста C4 целевого состояния системы в директорию Task2 в рамках пул-реквеста.

## Решение

```plantuml
@startuml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Context.puml

title Целевая архитектура системы "Медикаменте" - Context Level

Person(patient, "Пациент", "Клиент медицинского центра")
Person(doctor, "Врач", "Медицинский специалист")
Person(receptionist, "Администратор", "Сотрудник ресепшена")
Person(cashier, "Кассир", "Сотрудник кассы")
Person(accountant, "Бухгалтер", "Сотрудник бухгалтерии")
Person(warehouse_keeper, "Сотрудник склада", "Кладовщик")
Person(b_analyst, "Бизнес-Аналитик", "Специалист по анализу процессов и данных")

System_Ext(lab_system, "Лаборатория", "Внешняя система лабораторных исследований")
System_Ext(payment_gateway, "Платежный шлюз", "Внешняя система приема платежей")
System_Ext(sms_service, "SMS-сервис", "Внешний сервис уведомлений")
System_Ext(email_service, "Email-сервис", "Внешний сервис электронной почты")

System_Boundary(medicamente_boundary, "Медицинский центр Медикаменте") {
    
    System(patient_portal, "Портал пациентов", "Веб-портал и мобильное приложение для самостоятельной записи пациентов")
    System(staff_portal, "Портал сотрудников", "Внутренний портал для работы сотрудников")
    System(crm_system, "CRM система", "Система управления взаимоотношениями с клиентами")
    
    System(privacy_gateway, "Privacy Gateway", "Система обеспечения конфиденциальности данных\n- Псевдонимизация\n- Шифрование данных\n- Контроль доступа (RBAC/ABAC)\n- Аудит действий\n- Тегирование данных")
    
    System(core_medical_system, "Основная медицинская система", "Центральная система обработки медицинских данных\n- Медицинские карты\n- Записи к врачам\n- Результаты анализов")
    
    System(analytics_platform, "Аналитическая платформа", "Система аналитики и машинного обучения\n- BI-отчеты\n- ML-модели\n- Прогнозная аналитика")
    
    System(audit_system, "Система аудита", "Система мониторинга и аудита доступа к данным\n- Логирование действий\n- Детекция аномалий\n- Алертинг")
    System_Boundary(onec, "Контур систем 1С"){
        System(onec_accounting, "1С:Бухгалтерия предприятия", "Система финансового и бухгалтерского учета")
        System(onec_warehouse, "1С:Торговля и склад", "Система управления ТМЦ и складскими операциями")
    }
    System(notification_system, "Система уведомлений", "Центральная система отправки уведомлений")
}

' Взаимодействие пациентов
Rel(patient, patient_portal, "Записывается на прием,\nпросматривает результаты", "HTTPS")
Rel(patient_portal, privacy_gateway, "Персональные данные\n(зашифрованы)", "HTTPS/TLS")
Rel(privacy_gateway, core_medical_system, "Валидированные данные\n(с тегами конфиденциальности)", "HTTPS/TLS")

' Взаимодействие врачей
Rel(doctor, staff_portal, "Работает с медицинскими картами,\nназначает лечение", "HTTPS")
Rel(staff_portal, privacy_gateway, "Доступ к медицинским данным\n(с контролем доступа)", "HTTPS/TLS")

' Взаимодействие администраторов
Rel(receptionist, staff_portal, "Управляет записями,\nведет реестр пациентов", "HTTPS")
Rel(cashier, staff_portal, "Обрабатывает платежи", "HTTPS")
Rel(accountant, onec_accounting, "Ведет бухгалтерский учет", "HTTPS")
Rel(warehouse_keeper, onec_warehouse, "Управляет ТМЦ", "HTTPS")

' Взаимодействие аналитиков
Rel(b_analyst, analytics_platform, "Строит отчеты,\nанализирует данные", "HTTPS")

' Поток данных через Privacy Gateway
Rel(privacy_gateway, core_medical_system, "Обезличенные данные\nдля аналитики", "HTTPS/TLS")
Rel(privacy_gateway, analytics_platform, "Псевдонимизированные\nсобытия", "HTTPS/TLS")

' Система аудита
Rel(privacy_gateway, audit_system, "Логи доступа\nи операций", "HTTPS/TLS")
Rel(core_medical_system, audit_system, "События системы", "HTTPS/TLS")
Rel(analytics_platform, audit_system, "Аналитические запросы", "HTTPS/TLS")

' Внешние интеграции
Rel(core_medical_system, lab_system, "Результаты анализов\n(через защищенный API)", "HTTPS/TLS")
Rel(onec_accounting, payment_gateway, "Обработка платежей", "HTTPS/TLS")
Rel(core_medical_system, onec_accounting, "Данные о платежах пациентов", "HTTPS/TLS")
Rel(core_medical_system, onec_warehouse, "Данные о расходных материалах", "HTTPS/TLS")
Rel(notification_system, sms_service, "SMS-уведомления", "HTTPS/TLS")
Rel(notification_system, email_service, "Email-уведомления", "HTTPS/TLS")
Rel(patient_portal,payment_gateway,"Выполнение платежей", "HTTPS/TLS")

' Внутренние связи
Rel_D(core_medical_system, crm_system, "Данные о клиентах", "HTTPS/TLS")
Rel(core_medical_system, notification_system, "Уведомления пациентам", "HTTPS/TLS")
Rel(onec_warehouse, onec_accounting, "Внутриплатформенный обмен данными", "OLE")

@enduml
```