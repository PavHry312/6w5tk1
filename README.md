    using System;
    using System.Collections.Generic;
    using System.Linq;

    namespace FizycznyWisielec
    {

    class TerminFizyczny
    {
        public string Slowo { get; set; }      // Саме слово
        public string Kategoria { get; set; }  // Категорія (Механіка, Астрономія...)
        public string Opis { get; set; }       // Наукове визначення (підказка)

        public TerminFizyczny(string slowo, string kategoria, string opis)
        {
            Slowo = slowo.ToUpper();
            Kategoria = kategoria;
            Opis = opis;
        }
    }

    class Program
    {
        // База даних слів
        static List<TerminFizyczny> bazaTerminow = new List<TerminFizyczny>();

        static void Main(string[] args)
        {
            InicjalizujBazeDanych(); // Завантажуємо слова
            bool czyGracDalej = true;

            while (czyGracDalej)
            {
                Console.Clear();
                Console.WriteLine("=============================================");
                Console.WriteLine("   FIZYCZNY WISIELEC (PHYSICS HANGMAN) v2.0  ");
                Console.WriteLine("=============================================");
                Console.WriteLine("Wybierz kategorię:");
                Console.WriteLine("1. Mechanika (Mechanika)");
                Console.WriteLine("2. Astronomia (Astronomia)");
                Console.WriteLine("3. Matematyka (Matematyka)");
                Console.WriteLine("0. Wyjście (Вихід)");
                Console.Write("\nTwój wybór: ");

                string wybor = Console.ReadLine();

                if (wybor == "0")
                {
                    czyGracDalej = false;
                }
                else if (wybor == "1" || wybor == "2" || wybor == "3")
                {
                    RozpocznijGre(wybor);
                }
                else
                {
                    Console.WriteLine("Niepoprawny wybór. Naciśnij Enter...");
                    Console.ReadLine();
                }
            }
        }

        // Головна логіка гри
        static void RozpocznijGre(string kategoriaId)
        {
            // Вибір категорії
            string nazwaKategorii = "";
            if (kategoriaId == "1") nazwaKategorii = "Mechanika";
            if (kategoriaId == "2") nazwaKategorii = "Astronomia";
            if (kategoriaId == "3") nazwaKategorii = "Matematyka";

            // Фільтруємо слова за категорією (використання списків)
            var dostepneSlowa = bazaTerminow.Where(t => t.Kategoria == nazwaKategorii).ToList();
            
            // Випадковий вибір слова
            Random rnd = new Random();
            TerminFizyczny wybranyTermin = dostepneSlowa[rnd.Next(dostepneSlowa.Count)];

            char[] odgadniete = new char[wybranyTermin.Slowo.Length];
            for (int i = 0; i < odgadniete.Length; i++) odgadniete[i] = '_';

            int zycia = 6;
            int punkty = 100;
            bool czyWygrana = false;
            List<char> uzyteLitery = new List<char>();

            while (zycia > 0 && !czyWygrana)
            {
                Console.Clear();
                Console.WriteLine($"Kategoria: {nazwaKategorii} | Punkty: {punkty} | Życia: {zycia}");
                Console.WriteLine("---------------------------------------------");
                Console.WriteLine($"HASŁO: {string.Join(" ", odgadniete)}");
                Console.WriteLine($"Użyte litery: {string.Join(", ", uzyteLitery)}");
                Console.WriteLine("\n[Wpisz literę] LUB [wpisz 'POMOC' aby uzyskać definicję (-20 pkt)]");
                Console.Write("> ");

                string input = Console.ReadLine().ToUpper();

                // Логіка підказки (Hint)
                if (input == "POMOC")
                {
                    Console.WriteLine($"\nPODPOWIEDŹ: {wybranyTermin.Opis}");
                    punkty -= 20;
                    if (punkty < 0) punkty = 0;
                    Console.WriteLine("Naciśnij Enter, aby grać dalej...");
                    Console.ReadLine();
                    continue;
                }

                // Перевірка введення (чи це одна літера)
                if (input.Length != 1 || !char.IsLetter(input[0]))
                {
                    continue; 
                }

                char litera = input[0];

                if (uzyteLitery.Contains(litera))
                {
                    Console.WriteLine("Już użyłeś tej litery! Enter...");
                    Console.ReadLine();
                    continue;
                }

                uzyteLitery.Add(litera);

                if (wybranyTermin.Slowo.Contains(litera))
                {
                    // Відгадав літеру
                    for (int i = 0; i < wybranyTermin.Slowo.Length; i++)
                    {
                        if (wybranyTermin.Slowo[i] == litera)
                        {
                            odgadniete[i] = litera;
                        }
                    }
                }
                else
                {
                    // Не відгадав
                    zycia--;
                    punkty -= 10;
                    if (punkty < 0) punkty = 0;
                    Console.WriteLine($"Nie ma litery {litera}!");
                }

                // Перевірка перемоги
                if (!new string(odgadniete).Contains('_'))
                {
                    czyWygrana = true;
                }
            }

            // Кінець гри
            Console.Clear();
            if (czyWygrana)
            {
                Console.ForegroundColor = ConsoleColor.Green;
                Console.WriteLine($"GRATULACJE! Odgadłeś hasło: {wybranyTermin.Slowo}");
                Console.WriteLine($"Twój wynik końcowy: {punkty} pkt");
            }
            else
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("KONIEC GRY. Przegrałeś.");
                Console.WriteLine($"Prawidłowe hasło to: {wybranyTermin.Slowo}");
                Console.WriteLine($"Definicja: {wybranyTermin.Opis}");
            }
            Console.ResetColor();
            Console.WriteLine("\nNaciśnij Enter, aby wrócić do menu...");
            Console.ReadLine();
        }

        // Тут ми наповнюємо нашу "базу" термінами
        static void InicjalizujBazeDanych()
        {
            bazaTerminow.Add(new TerminFizyczny("PRZYSPIESZENIE", "Mechanika", "Wektorowa wielkość fizyczna wyrażająca zmianę prędkości w czasie."));
            bazaTerminow.Add(new TerminFizyczny("BEZWŁADNOŚĆ", "Mechanika", "Właściwość materii polegająca na zachowaniu stanu spoczynku lub ruchu jednostajnego."));
            bazaTerminow.Add(new TerminFizyczny("TARCIE", "Mechanika", "Siła oporu ruchu występująca na styku dwóch powierzchni."));
            
            bazaTerminow.Add(new TerminFizyczny("KWAZAR", "Astronomia", "Bardzo jasny obiekt gwiazdopodobny, będący aktywnym jądrem galaktyki."));
            bazaTerminow.Add(new TerminFizyczny("EKLIPTYKA", "Astronomia", "Wielkie koło na sferze niebieskiej, po którym pozornie porusza się Słońce."));
            bazaTerminow.Add(new TerminFizyczny("CZARNA DZIURA", "Astronomia", "Obszar czasoprzestrzeni, z którego nic, nawet światło, nie może uciec."));
            
            bazaTerminow.Add(new TerminFizyczny("CAŁKA", "Matematyka", "Ogólne określenie wielu różnych, choć powiązanych ze sobą pojęć analizy matematycznej."));
            bazaTerminow.Add(new TerminFizyczny("MACIERZ", "Matematyka", "Układ liczb, symboli lub wyrażeń zapisanych w postaci prostokątnej tablicy."));
            bazaTerminow.Add(new TerminFizyczny("GRANICA", "Matematyka", "Podstawowe pojęcie analizy matematycznej opisujące zachowanie funkcji w pobliżu danego punktu."));
        }
    }
}
