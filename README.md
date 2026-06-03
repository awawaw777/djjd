#include "colors.inc"
#include "textures.inc"

global_settings {
    ambient_light rgb <0.15, 0.15, 0.15>   // повышен для ровного фона (было 0.05)
}

camera {
    location <1, 12, -390>
    look_at <-5, 0, 20>
}

// Оси координат (оставлены как есть)
box { <0, -10, 0>, < 0.2, 100, 0.2> texture { pigment { color Black } } }
box { <0, -10, 0>, < 100, -9.8, 0.2> texture { pigment { color Black } } }
box { <0, -10, 0>, < 0.2, -9.8, 100> texture { pigment { color Blue } } }

// Mirror
box { <-5, 0, 29.5>, < 5, 13, 30> texture { pigment { color White } finish { reflection 1 } } }

// ========== НОВОЕ РАВНОМЕРНОЕ ОСВЕЩЕНИЕ (вместо старых ламп) ==========
// Удалены все старые light_source и их боксы-плафоны.
// Ниже идёт цепочка мягких источников вдоль коридора от Z = -320 до +30 с шагом 22.
// Чтобы сохранить видимость твоих плафонов, я оставил их (box с filter 0.8) 
// как декоративные объекты – они появляются в тех же местах, где были.

#macro CorridorLight(X, Z, Y)
    light_source {
        <X, Y, Z>
        color rgb <1.2, 1.15, 1.1>   // тёплый белый свет
        fade_distance 14
        fade_power 2                 // квадратичное затухание (плавное)
        area_light <1.5, 0, 0>, <0, 0, 1.5>, 4, 4
        adaptive 1
        jitter
    }
#end

// Расставляем источники по центру коридора (X = 0, высота 8 – потолок)
#local StartZ = -320;
#local EndZ   = 30;
#local Step   = 22;
#local Zpos = StartZ;
#while (Zpos <= EndZ)
    CorridorLight(0, Zpos, 8)
    // Декоративный плафон (как у тебя, но сдвинут по X на 0)
    box { 
        < -0.8, 3-5, Zpos-0.8 >, < 0.8, 13+5, Zpos+0.8 >
        texture { 
            pigment { color White filter 0.8 }
            finish { diffuse 1 }
        }
        no_shadow
    }
    #local Zpos = Zpos + Step;
#end

// Дополнительная подсветка снизу (по бокам) – опционально, делает свет ещё ровнее
#macro WallLight(X, Z, Y)
    light_source {
        <X, Y, Z>
        color rgb <0.9, 0.85, 0.8>
        fade_distance 10
        fade_power 2
        area_light <0.8,0,0>, <0,0,0.8>, 2, 2
        jitter
    }
#end

// Включаем бра на стенах (как в оригинале были разнесены по X = 14 и -24)
// Здесь я ставлю их на X = -5 и +5, на высоте 1.5 от пола
#local Zpos = StartZ + 11;
#while (Zpos <= EndZ - 11)
    WallLight(-5, Zpos, 1.5)
    WallLight( 5, Zpos, 1.5)
    #local Zpos = Zpos + 22;
#end

// ========== Твои старые объекты (ландшафт, сфера, цилиндр) остались без изменений ==========
plane { z, 30 pigment { brick } }
plane { x, 15 pigment { brick } }
plane { x,-25 pigment { brick } }
plane{ y,-10 pigment { checker Black,Gray scale 3 } }
plane{ y, 30 pigment { wood } }
box { <0, 0, 0>, < 10, 1, 16> pigment { wood } }
box { <9, -10, 0>, < 10, 0, 1> pigment { wood } }
box { <0, -10, 0>, < 1, 0, 1> pigment { wood } }
box { <0, -10, 15>, < 1, 0, 16> pigment { wood } }
box { < 9, -10, 15>, <10, 0, 16> pigment { wood } }
sphere { <4,2,7>, 1 texture { pigment { color White } finish { reflection 1 } } }
cylinder { <4, 1, 3>, <4, 3, 3>, 0.5 texture { pigment { color Green filter 0.8 } finish { diffuse 1 } } }
