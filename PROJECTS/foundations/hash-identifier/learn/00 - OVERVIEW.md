# Ідентифікатор хешу

## Що це таке

Невелика програма на Python, яка аналізує рядок із дивними символами та повідомляє, який це, ймовірно, тип криптографічний хеш.

Наприклад : 

```
5f4dcc3b5aa765d61d8327deb882cf99
```

і програма повідомляє ''це хеш MDM5''

<img width="656" height="512" alt="Screenshot_2026-07-09_14_30_35" src="https://github.com/user-attachments/assets/f83e9db0-4463-4f74-8729-0eb36089e5f2" />

Але перед тим, як писати відразу команду для пошуку, що це за хеш. Я скопіювала повністю репозиторій з якого беру матеріали та практикуюсь і встановила файл в якому знаходить ця невеличка програма. На скріншоті видно, як я перешла у папку і написала команду, щоб дізнатись, що це за хеш і результат. Ось репозиторій : https://github.com/CarterPerez-dev/Cybersecurity-Projects/tree/main/PROJECTS

##  Навіщо це?

Хеші виглядають нісенітницею, але вони не випадкові — кожен хеш містить підказки про те, як він був створений. Як тільки буде відомий  алгоритм (MD5, SHA-256, bcrypt, Argon2 тощо), його можна передати хеш інструменту для злому, такому як hashcat або John the Ripper , і почати намагатися відновити оригінальний пароль.

Але ось у чому річ: hashcat потребує, щоб йому вказали  алгоритм. Він має понад 400 режимів хешування , кожен з яких має різний номер. Режим 0 – це MD5. Режим 100 – це SHA-1. Режим 3200 – це bcrypt. Якщо обрати неправильний режим, hashcat залишиться там назавжди і нічого не знайде. Тож перед зломом ідентифікуєте. Це цей інструмент.

## Чого я зможу навчитись? 

**Ідеї ​​безпеки:**

- Що насправді являє собою криптографічний хеш (функція, яка перетворює будь-які вхідні дані на фіксовану довжину, яку неможливо змінити).
- Три сигнали, які кожен хеш розкриває про себе: його *префікс* , його *довжина* та його *набір символів* .
- Чому сучасні хеші паролів ( $2b$..., $argon2id$...) навмисно оголошують себе , а старі швидкі хеші (MD5, SHA-1) – ні.
- Різниця між швидким хешем (створеним для швидкості, жахливим для паролів) та повільним хешем (створеним навмисно, щоб запобігти злому).
- Чому ніколи не можна відновити пароль з хешу, можна лише вгадати пароль і перевірити, чи збігається його хеш.
  
**Ідеї ​​з Python:**

- Як прочитати файл Python зверху вниз і зрозуміти, що він робить.
- Що importробить і де закінчується стандартна бібліотека та починаються сторонні пакети.
- Функції, підказки щодо типів ( str, int, list[str]) та що -> boolозначає після сигнатури функції.
- @dataclass— скорочений спосіб створення невеликих об’єктів, схожих на платівки.
- frozenset, dict, list, tuple— основні контейнери Python та коли який з них вибирати.
- Як насправді запускається інструмент командного рядка ( if __name__ == "__main__"рядок внизу).
- Як працює тестовий файл і чому поруч із кожною функцією в основному коді є тести.
  
**Інструменти, з якими я далі буду працювати:**

- uv— сучасний менеджер пакетів Python. Схожий на той pip, але приблизно в 100 разів швидший.
- just— виконавець команд. Замість того, щоб запам'ятовувати довгі команди, ви друкуєте just testабо just run.
- rich— бібліотека, яка друкує гарну кольорову таблицю в кінці.
- pytest— Виконавець тестів Python.
- ruff+ mypy+ pylint— лінтери, які кричать на вас, якщо ваш код неправильний, повільний або недбалий.

## Іще трішки практики

# bcrypt — modern password hash, announces itself with the $2b$ prefix
just run -- '$2b$12$EixZaYVK1fsbw1ZfbX3OXePaWxn96p36WQNQy.uK4Of2T7G'   --> коли хеш починається з $, його потрібно обернути в одинарні лапки ( '$2b$...'). Без лапок ваша оболонка спробує розширити його $2як змінну оболонки та розділити хеш на частини. Це властиво оболонкі, а не Python — кожна оболонка Unix робить це.

# SHA-256 — 64 hex characters
just run -- e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855

# A JWT (this is NOT a hash, but the tool will say so politely)
just run -- eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIn0.dozjgNryP4J3jVmNHl0w5N_XgL0n3I9PlFUP0THsR8U

# Total garbage — the tool will say "no idea" rather than guess
just run -- helloworld

<img width="766" height="593" alt="Screenshot_2026-07-09_14_58_35" src="https://github.com/user-attachments/assets/bae722ee-2df5-459b-bbf7-3525ad3400e9" />

<img width="700" height="587" alt="Screenshot_2026-07-09_14_58_40" src="https://github.com/user-attachments/assets/0e88f67f-4f3a-468d-ad56-7209bff576d1" />

## Макет проєкту

hash-identifier/
├── hash_identifier.py        the whole tool — one file, ~680 lines
├── test_hash_identifier.py   tests for every behavior the tool claims to have
├── install.sh                one-shot setup script
├── justfile                  shortcuts for run / test / lint / format
├── pyproject.toml            project config: dependencies, linter rules, etc.
├── README.md                 short pointer to this learn/ folder
├── learn/                    you are here
│   ├── 00-OVERVIEW.md        quick start (this file)
│   ├── 01-CONCEPTS.md        what hashes are and how identification works
│   ├── 02-ARCHITECTURE.md    how the code is structured, with diagrams
│   ├── 03-IMPLEMENTATION.md  line-by-line walkthrough of the code
│   └── 04-CHALLENGES.md      extension ideas if you want to go further
└── assets/                   images, screenshots
