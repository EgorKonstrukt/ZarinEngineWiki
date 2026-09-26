# NetworkIdentity

Сетевая карточка существования сущности: кто она на проводе, кто владеет, какой префаб инстанцировать удалённо, переживает ли смены сцен.

| Группа / поле инспектора | Смысл |
|---|---|
| Network Identity: Net ID | Уникальный id сессии, назначается на спавне |
| Owner ID | Id пира с правом записи |
| Prefab ID | Какой префаб инстанцируют ремоуты |
| Authority | AuthorityMode — owner-driven или server-authoritative |
| Is Local Player | Флаг аватара этого пира |
| Dont Destroy | Переживает загрузки сцен (менеджеры, состояние сессии) |

API: `transfer_ownership(peer_id)`, `request_ownership()`, `apply_owner_change(...)` — протокол передачи для подборов, техники и брошенных предметов.

Модель авторитетности: owner-driven компоненты принимают записи от пира Owner ID; server-потоки идут через хост. `AuthorityMode` выбирает позу на сущность — игроки owner-driven, двери и счёт серверные.

Каждому сетевому префабу ровно один NetworkIdentity в корне; sync-компоненты (transform, rigidbody, animator, variables) вешаются рядом и ключеваны Net ID. `Is Local Player` гейтит скрипты ввода — префаб один, клавиши читает только владелец.

Паттерн передачи: водитель сел в машину → `request_ownership()` → хост аппрувит → `transfer_ownership(driver)` → трансформ следует за водителем до возврата на выходе.

Диагностика: дубликаты на ремоутах — mismatch prefab id или нет identity в корне; ввод на всех клонах — нет гейта `Is Local Player`; передача игнорится — запрос не авторитету, роутите через хост.

Связанное: NetworkManager (спавны назначают id), NetworkTransform/Rigidbody/Animator/Variables (синк от этого id), NetworkPlayer (близнец данных пиров).
