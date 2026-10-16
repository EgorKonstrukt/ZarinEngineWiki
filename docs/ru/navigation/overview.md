# Навигация и NavAgent

Сеточная навигация (`NavWorld`) с отложенным GPU-запросом пути; `NavAgent` ведёт сущность к цели.

## Свойства / API

| Поле | Умолчание | Смысл |
|---|---|---|
| `agent_radius / agent_height / agent_padding` | 0.5 / 2.0 / 0.0 | Капсула и отступ от стен |
| `max_climb / max_slope` | 0.5 / 45.0 | Ступень и склон |
| `max_speed / max_angular_speed` | 5.0 / 360.0 | Лимиты руления |
| `use_physical_controller / flying / auto_move / show_path_cells` | true / false / false / false | Физ-контроллер, полёт, автодвижение, дебаг |
| `target_x / target_y / target_z / target_entity_id` | 0 / 0 / 0 / "" | Точка или сущность цели |
| `repath_interval / arrival_distance` | 1.0 / 0.5 | Период перестроения и радиус прибытия |
| `nav_resolution / nav_world_size` | 512 / 500.0 | Плотность сетки и размер мира |
| `set_target / set_target_entity / stop / has_path / current_path` | — | Рантайм API через `find_path_gpu_deferred` |

## Использование в редакторе

1. Добавьте `NavAgent`; настройте радиус/высоту/скорость.
2. Задайте XYZ цели или `target_entity_id`, включите `auto_move`; кнопки `Set Target` / `Stop`.
3. `show_path_cells` подсвечивает путь по сетке.

## Использование в коде

```python
from core.maths.math3d import Vec3

agent = entity.get_component_by_name("NavAgent")
agent.set_target(Vec3(10.0, 0.0, 5.0))
agent.set_target_entity(player_entity)
if not agent.has_path():
    agent.stop()
```

## Связанные

- Террейн, Constraints, физ-контроллер

## Проверено по

- `core/components/navigation/navagent/component.py`, `core/navigation/world.py`
