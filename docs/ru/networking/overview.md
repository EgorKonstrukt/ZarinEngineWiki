# Обзор сетевой части

Две независимые подсистемы делят транспортный слой:

- Геймплейный неткод (компоненты + NetworkPlugin): синхронизация сущностей сессии — трансформы, rigidbody, состояние аниматора, кастомные переменные, спавны и RPC. Авторитетность на компонент: авторитетная или owner-driven; транспорт внизу заменяем.
- Коллаборация редакторов (CollabServer / CollabClient): несколько редакторов в одной сессии видят курсоры, позиции камер, выделения и гизмо-операции вживую, со снапшотами сцен, вотчером ассетов и совместным редактированием скриптов.

Обе едут на `core/network`: TCP-транспорт с msgpack-фреймингом (`protocol.MessageType`, `transport`), пара `GameServer`/`GameClient`, парольные комнаты (`server.set_password/set_room/set_max_clients`), хелперы relay и UPnP для NAT, модуль `rpc` для удалённых вызовов.

Честный статус: коллаборация — рабочая real-time система; геймплейная сторона поставляет компоненты, менеджер и заглушки плагина (`host/connect/send/broadcast/poll`) под любой транспорт — WebRTC, ENet или сырые сокеты. Проектируйте геймплей против API компонентов (владение, снапшоты, send rate); проводной формат внизу остаётся заменяемым.

Страницы: транспорт и протокол, сетевой плагин, NetworkManager, NetworkIdentity, NetworkTransform, NetworkRigidbody, NetworkAnimator, NetworkVariables, NetworkSpawn, NetworkPlayer, коллаборация, RemoteCollaborator.

Рецепт первой сессии: пустая сцена → сущность NetworkManager (порт 7777, назначен player prefab, автоспавн вкл) → префаб игрока с NetworkIdentity + NetworkTransform + NetworkPlayer → host на одной машине, connect со второй → оба спавна появляются и двигаются.
