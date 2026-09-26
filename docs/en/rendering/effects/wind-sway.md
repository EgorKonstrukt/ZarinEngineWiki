# Wind Sway

Per-object effect through the ObjectFx pass (unlike PostFX, which processes the whole frame). Built on the shared ObjectEffect base; scripts drive it by animating progress/intensity floats.

## How it works

Gentle generic vertex sway for grass, cloth and hanging props.

## Parameters

Strength, frequency, phase variation.

## Tips

Grass fields, banners, chandeliers. Phase variation prevents synchronized marching.

## Cost

Negligible vertex math.

Related: [Object Effects](../effects.md), Materials, Script Examples.
