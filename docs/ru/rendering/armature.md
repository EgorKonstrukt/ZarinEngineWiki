# Арматура Armature

Контейнер иерархии костей, ведущий скинированные меши и цепочки PhysBone.

| Группа / поле инспектора | Смысл |
|---|---|
| Bone: Bone Name | Читаемое имя сустава |
| Bone: Bone Index | Индекс на стороне солвера |
| Armature: Bone Count | Всего суставов |
| Armature: Root Bone | Корень иерархии |

Animator пишет позы в арматуру; SkinnedMeshRenderer и PhysBone их читают. Держите имена костей стабильными — ретаргетинг и оверрайды префабов матчатся по имени.

Связанное: Skinned Mesh Renderer, PhysBone, клипы и Animator.
