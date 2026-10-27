+++
date = '2026-10-28T01:32:34+05:30'
draft = false
title = 'Introduction'
+++

Coding Math is 58 video series on youtube

stuffs used in the series: sublime text + javascript + chrome,html5
canvas for rendering
Your goal is to re-do all but with non-browser GUI

1. Getting a display of display's width and height and drawing a filled-rectangle
2. Draw 100 random line on canvas


## My solution

```
#include "raylib.h"

int main(void) {
    SetConfigFlags(FLAG_WINDOW_RESIZABLE);   // must come before InitWindow
    InitWindow(800, 600, "Canvas");
    SetTargetFPS(60);
    MaximizeWindow();

    while (!WindowShouldClose()) {
        BeginDrawing();
        ClearBackground(RAYWHITE);

        Rectangle rect = { 200.0f, 150.0f, 400.0f, 300.0f };

        DrawRectangleRec(rect, BLUE);
        DrawRectangleLinesEx(rect, 5, RED); 
        DrawFPS(10, 10);

        EndDrawing();
    }

    CloseWindow();
    return 0;
}
```

