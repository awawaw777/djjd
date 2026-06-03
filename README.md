# djjd
// ============================================================
// Коридор с равномерным освещением (POV-Ray)
// ============================================================

#include "colors.inc"
#include "textures.inc"
#include "shapes.inc"

global_settings {
    ambient_light rgb <0.18, 0.18, 0.18>   // базовый заполняющий свет
    assumed_gamma 1.0
}

// -------------------- Камера --------------------
camera {
    location <1, 5, -390>     // чуть ниже, чтобы лучше видеть коридор
    look_at   <0, 4, 20>
    angle 70
}

// -------------------- Геометрия коридора --------------------
#declare FloorY = 0;
#declare CeilY  = 8;
#declare LeftX  = -6;
#declare RightX = 6;
#declare BackZ  = -340;
#declare FrontZ = 40;

// Пол
box {
    <LeftX, FloorY, BackZ>, <RightX, FloorY+0.2, FrontZ>
    texture {
        pigment { color rgb <0.6,0.55,0.5> }
        finish { diffuse 0.8 specular 0.2 roughness 0.1 }
    }
}

// Потолок
box {
    <LeftX, CeilY-0.1, BackZ>, <RightX, CeilY, FrontZ>
    texture {
        pigment { color White }
        finish { diffuse 0.7 }
    }
}

// Левая стена
box {
    <LeftX, FloorY, BackZ>, <LeftX+0.3, CeilY, FrontZ>
    texture {
        pigment { color rgb <0.85,0.82,0.78> }
        finish { diffuse 0.8 }
    }
}

// Правая стена
box {
    <RightX-0.3, FloorY, BackZ>, <RightX, CeilY, FrontZ>
    texture {
        pigment { color rgb <0.85,0.82,0.78> }
        finish { diffuse 0.8 }
    }
}

// Задняя стена (в глубине)
box {
    <LeftX, FloorY, BackZ>, <RightX, CeilY, BackZ+0.3>
    texture {
        pigment { color Gray50 }
        finish { diffuse 0.6 }
    }
}

// -------------------- Зеркало в конце коридора (как в оригинале) --------------------
box {
    <-5, 0, 29.5>, <5, 13, 30>
    texture {
        pigment { color White }
        finish { reflection 1 diffuse 0 }
    }
}

// -------------------- Освещение: равномерный ряд встроенных светильников --------------------
// Макрос для одного светильника с мягким площадным светом
#macro CeilingLight(X, Z)
    // Невидимый источник света (area_light для мягких теней)
    light_source {
        <X, CeilY-0.05, Z>
        color rgb <1.1, 1.05, 1.0>   // тёплый белый
        fade_distance 12
        fade_power 2
        area_light <1.2, 0, 0>, <0, 0, 1.2>, 4, 4
        adaptive 1
        jitter
        looks_like {
            sphere { <0,0,0>, 0.25
                texture {
                    pigment { color rgbt <1,1,1,0.6> }
                    finish { diffuse 0.2 ambient 0.8 }
                }
            }
        }
    }
#end

// Расставляем светильники вдоль оси Z от -320 до 30 с шагом 22
// Шаг выбран так, чтобы световые пятна перекрывались, не оставляя тёмных зон.
#local StartZ = -320;
#local EndZ   = 30;
#local Step   = 22;
#local Zpos = StartZ;
#while (Zpos <= EndZ)
    CeilingLight(0, Zpos)      // по центру коридора
    #local Zpos = Zpos + Step;
#end

// Дополнительные бра на стенах (опционально) – для боковой подсветки, делают свет ещё ровнее
// Но они не обязательны. Раскомментируйте, если хотите.
/*
#macro WallLight(X, Z, Y)
    light_source {
        <X, Y, Z>
        color rgb <0.8, 0.75, 0.7>
        fade_distance 8
        fade_power 2
        area_light <0.5,0,0>, <0,0,0.5>, 2, 2
        jitter
    }
#end
#local Zpos = StartZ + 11;
#while (Zpos <= EndZ - 11)
    WallLight(LeftX+0.2, Zpos, 1.5)
    WallLight(RightX-0.2, Zpos, 1.5)
    #local Zpos = Zpos + 22;
#end
*/

// -------------------- Декоративные плафоны (видимые) --------------------
// Маленькие белые кубики на потолке, скрывающие источники света
#local Zpos = StartZ;
#while (Zpos <= EndZ)
    box {
        <-0.6, CeilY-0.15, Zpos-0.6>, <0.6, CeilY-0.05, Zpos+0.6>
        texture {
            pigment { color White }
            finish { diffuse 0.9 specular 0.3 }
        }
        no_shadow   // чтобы не блокировали свет
    }
    #local Zpos = Zpos + Step;
#end

// -------------------- (Необязательно) лёгкий туман для глубины --------------------
fog {
    distance 300
    color rgb <0.9,0.9,0.95>
    fog_type 2
}
