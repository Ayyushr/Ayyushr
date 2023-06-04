#include <iostream>

#include <vector>

#include <algorithm>

#include <cmath>

#include <SDL2/SDL.h>

using namespace std;

const int width = 640;

const int height = 480;

class Game {

public:

  Game() {

    // Initialize the game world.

    for (int i = 0; i < width * height; i++) {

      world[i] = 0;

    }

    // Create the player.

    player.x = width / 2;

    player.y = height / 2;

    player.speed = 10;

    // Create the enemies.

    for (int i = 0; i < 10; i++) {

      enemies.push_back(Enemy());

    }

    // Initialize SDL.

    SDL_Init(SDL_INIT_VIDEO);

    // Create the window.

    window = SDL_CreateWindow("Spider-Man", SDL_WINDOWPOS_UNDEFINED, SDL_WINDOWPOS_UNDEFINED, width, height, SDL_WINDOW_SHOWN);

    // Create the renderer.

    renderer = SDL_CreateRenderer(window, -1, SDL_RENDERER_ACCELERATED);

  }

  void Update() {

    // Get the player's input.

    SDL_Event event;

    while (SDL_PollEvent(&event)) {

      switch (event.type) {

        case SDL_QUIT:

          running = false;

          break;

        case SDL_KEYDOWN:

          switch (event.key.keysym.sym) {

            case SDLK_LEFT:

              player.x -= player.speed;

              break;

            case SDLK_RIGHT:

              player.x += player.speed;

              break;

            case SDLK_UP:

              player.y -= player.speed;

              break;

            case SDLK_DOWN:

              player.y += player.speed;

              break;

          }

          break;

      }

    }

    // Check for collisions between the player and the enemies.

    for (Enemy &enemy: enemies) {

      if (player.x == enemy.x && player.y == enemy.y) {

        // The player has collided with an enemy.

        cout << "You lost!" << endl;

        exit(0);

      }

    }

    // Clear the screen.

    SDL_RenderClear(renderer);

    // Draw the game world.

    for (int i = 0; i < width * height; i++) {

      if (world[i] == 1) {

        SDL_RenderDrawPoint(renderer, i % width, i / width);

      }

    }

    // Draw the player.

    SDL_RenderDrawRect(renderer, &player.rect);

    // Draw the enemies.

    for (Enemy &enemy: enemies) {

      SDL_RenderDrawRect(renderer, &enemy.rect);

    }

    // Update the screen.

    SDL_RenderPresent(renderer);

  }

private:

  struct Enemy {

    int x;

    int y;

    SDL_Rect rect;

  };

  vector<int> world;

  Enemy player;

  vector<Enemy> enemies;

  bool running = true;

  SDL_Window *window;

  SDL_Renderer *renderer;

};

int main() {

  Game game;

  while (game.running) {

    game.Update();

  }

  // Cleanup SDL.

  SDL_DestroyRenderer(game.renderer);

  SDL_DestroyWindow(game.window);

  SDL_Quit();

  return 0;

}
