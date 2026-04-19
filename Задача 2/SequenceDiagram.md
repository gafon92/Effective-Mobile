```mermaid
sequenceDiagram
    autonumber
    actor Seller as Продавец
    participant UI as Личный кабинет (Frontend)
    participant Backend as Сервер (Backend/БД)

    Seller->>UI: Нажимает "Добавить товар"
    UI->>Backend: GET: Запрос списка категорий
    Backend-->>UI: Возврат дерева категорий
    UI-->>Seller: Отображение окна выбора

    Seller->>UI: Выбирает категорию
    UI->>Backend: GET: Запрос шаблона для категории
    Backend-->>UI: Возврат атрибутов (обяз. и опциональные)
    UI-->>Seller: Отображение формы карточки товара

    Note over Seller, UI: Заполнение полей (Название, Цена, Остатки, Фото)

    opt Фоновое кэширование (А3)
        UI->>UI: Сохранение черновика в LocalStorage
    end

    Seller->>UI: Нажимает "Предварительный просмотр"
    UI-->>Seller: Рендер витринного вида товара

    Seller->>UI: Нажимает "Опубликовать"
    
    alt Ошибка валидации (А1)
        UI-->>Seller: Подсветка пустых полей (без отправки на сервер)
    else Успешная валидация
        UI->>Backend: POST: Отправка данных товара
        Backend->>Backend: Валидация на стороне сервера
        Backend->>Backend: Присвоение статуса "На модерации"
        Backend->>Backend: Сохранение в БД
        Backend-->>UI: Ответ 200 OK (Успех)
        UI-->>Seller: Уведомление об успешной отправке
    end