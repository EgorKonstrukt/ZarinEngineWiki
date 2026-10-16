# Сеть

Две рабочие подсистемы на одном TCP/msgpack-транспорте (`core/network`): геймплейный неткод и коллаборация редактора. Геймплейный транспорт реализован — `GameServer`/`GameClient` поверх TCP с диспетчеризацией `NET_*`-сообщений — не заглушка (см. примечание о фактах в странице обзора).

- Обзор сетевой части — две подсистемы, честный статус, рецепт первой сессии.
- Транспорт и протокол — синглтон `Transport`, фрейминг `MessageType`, модуль RPC.
- Сетевой плагин — связка `NetworkPlugin`: host/connect/send/broadcast/poll.
- NetworkManager — компонент сессии, таблицы спавнов, диспетч по типам.
- NetworkIdentity — net id, владение, режимы авторитетности.
- NetworkTransform — отправка по порогам, интерполяция, телепорт.
- NetworkRigidbody — синк скоростей, путь через fixed-update.
- NetworkAnimator — синк состояний/параметров, триггеры.
- NetworkVariables — репликация кастомных переменных.
- NetworkSpawn — API спавна префабов.
- NetworkPlayer — данные на пира (ready/score/team/name).
- Коллаборация — сессии `CollaborationManager` в редакторе.
- RemoteCollaborator — компонент-представление удалённого пира.
