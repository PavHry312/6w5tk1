using System;
using System.Collections.Generic;
using System.Threading;

class Program
{
    static int width = 40;
    static int height = 20;

    static int x = width / 2;
    static int y = height / 2;

    static int foodX;
    static int foodY;

    static int score = 0;
    static string direction = "RIGHT";

    static List<(int, int)> snake = new List<(int, int)>();

    static void Main()
    {
        Console.CursorVisible = false;
        Console.SetWindowSize(width + 2, height + 4);

        snake.Add((x, y));
        GenerateFood();

        while (true)
        {
            if (Console.KeyAvailable)
            {
                ConsoleKey key = Console.ReadKey(true).Key;
                ChangeDirection(key);
            }

            Move();
            Draw();
            Thread.Sleep(180); // ⏳ повільніше
        }
    }

    static void ChangeDirection(ConsoleKey key)
    {
        if (key == ConsoleKey.LeftArrow && direction != "RIGHT") direction = "LEFT";
        if (key == ConsoleKey.RightArrow && direction != "LEFT") direction = "RIGHT";
        if (key == ConsoleKey.UpArrow && direction != "DOWN") direction = "UP";
        if (key == ConsoleKey.DownArrow && direction != "UP") direction = "DOWN";
    }

    static void Move()
    {
        switch (direction)
        {
            case "LEFT": x--; break;
            case "RIGHT": x++; break;
            case "UP": y--; break;
            case "DOWN": y++; break;
        }

        // 🧱 зіткнення з рамкою
        if (x <= 0 || x >= width - 1 || y <= 0 || y >= height - 1)
            GameOver();

        snake.Insert(0, (x, y));

        if (x == foodX && y == foodY)
        {
            score++;
            GenerateFood();
        }
        else
        {
            snake.RemoveAt(snake.Count - 1);
        }
    }

    static void Draw()
    {
        Console.Clear();

        DrawBorder();

        // 🍎 їжа
        Console.SetCursorPosition(foodX, foodY);
        Console.Write("●");

        // 🐍 змійка
        foreach (var part in snake)
        {
            Console.SetCursorPosition(part.Item1, part.Item2);
            Console.Write("■");
        }

        Console.SetCursorPosition(0, height + 2);
        Console.Write("Score: " + score);
    }

    static void DrawBorder()
    {
        for (int i = 0; i < width; i++)
        {
            Console.SetCursorPosition(i, 0);
            Console.Write("#");
            Console.SetCursorPosition(i, height - 1);
            Console.Write("#");
        }

        for (int i = 0; i < height; i++)
        {
            Console.SetCursorPosition(0, i);
            Console.Write("#");
            Console.SetCursorPosition(width - 1, i);
            Console.Write("#");
        }
    }

    static void GenerateFood()
    {
        Random rand = new Random();
        foodX = rand.Next(1, width - 2);
        foodY = rand.Next(1, height - 2);
    }

    static void GameOver()
    {
        Console.Clear();
        Console.WriteLine("GAME OVER");
        Console.WriteLine("Score: " + score);
        Console.ReadKey();
        Environment.Exit(0);
    }
}
