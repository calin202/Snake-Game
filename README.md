import curses
import random
import time

WIDTH, HEIGHT = 40, 20  # Increased playing area!

def main(stdscr):
    curses.curs_set(0)
    stdscr.nodelay(1)
    stdscr.timeout(100)

    snake = [(WIDTH//2, HEIGHT//2)]
    direction = (1, 0)
    food = (random.randint(1, WIDTH-2), random.randint(1, HEIGHT-2))
    score = 0

    while True:
        # Input
        key = stdscr.getch()
        # Try both arrow keys and WASD
        if key in [curses.KEY_UP, ord('w'), ord('W')] and direction != (0, 1):
            direction = (0, -1)
        elif key in [curses.KEY_DOWN, ord('s'), ord('S')] and direction != (0, -1):
            direction = (0, 1)
        elif key in [curses.KEY_LEFT, ord('a'), ord('A')] and direction != (1, 0):
            direction = (-1, 0)
        elif key in [curses.KEY_RIGHT, ord('d'), ord('D')] and direction != (-1, 0):
            direction = (1, 0)

        # Move
        new_head = (snake[0][0] + direction[0], snake[0][1] + direction[1])
        if (new_head[0] < 0 or new_head[0] >= WIDTH or
            new_head[1] < 0 or new_head[1] >= HEIGHT or
            new_head in snake):
            stdscr.addstr(HEIGHT//2, WIDTH//2-5, f"Game Over!")
            stdscr.addstr(HEIGHT//2+1, WIDTH//2-7, f"Final Score: {score}")
            stdscr.refresh()
            time.sleep(2)
            break

        snake.insert(0, new_head)
        if new_head == food:
            score += 1
            while True:
                food = (random.randint(1, WIDTH-2), random.randint(1, HEIGHT-2))
                if food not in snake:
                    break
        else:
            snake.pop()

        stdscr.clear()
        # Draw border
        for x in range(WIDTH):
            stdscr.addch(0, x, '#')
            stdscr.addch(HEIGHT-1, x, '#')
        for y in range(HEIGHT):
            stdscr.addch(y, 0, '#')
            stdscr.addch(y, WIDTH-1, '#')
        # Draw snake
        for (x, y) in snake:
            stdscr.addch(y, x, 'O')
        # Draw food
        stdscr.addch(food[1], food[0], 'X')
        # Draw score
        stdscr.addstr(HEIGHT, 0, f"Score: {score}")

        stdscr.refresh()

if __name__ == "__main__":
    curses.wrapper(main)
