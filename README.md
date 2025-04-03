# tests2

2. remove – Dzēst grāmatu  
__Loģika:__  
   - Tiek ielādētas visas grāmatas.  
   - Tiek filtrētas tās, kas neatbilst dzēšamajai grāmatai.  
   - Fails tiek pārrakstīts bez izdzēstās grāmatas.

3. __find__ – Atrast grāmatu  
_Kā tas darbojas:_  
    - Pieprasa grāmatas nosaukumu.  
    - Meklē to sarakstā un, ja atrod, izvada informāciju.  
    __Ka ir realizets koda:__  
    ```  
    public static Book findBookByTitle(List<Book> books, String title) {
        return books.stream()
        .filter(book -> book.getTitle().equalsIgnoreCase(title))
        .findFirst()
        .orElse(null);
    }  
    ```  
   __Loģika:__  
   1. _Filtrēšana (filter)_  
        - __Metode:__ .filter(book -> book.getTitle().equalsIgnoreCase(title))  
        - __Darbība:__ Iziet cauri visām sarakstā esošajām grāmatām (books) un atstāj tikai tās, kuru nosaukums (getTitle()) sakrīt ar meklēto (title), ignorējot lielos un mazos burtus (equalsIgnoreCase).  
   2. _Pirmā atrastā grāmata (findFirst)_  
       - __Metode:__ .findFirst()  
       - __Darbība:__ Ja sarakstā ir vairākas grāmatas ar tādu pašu nosaukumu, __atgriež tikai pirmo atrasto.__  
   3. _Rezultāts (orElse(null))_  
       - __Metode:__ .orElse(null)  
       - __Darbība:__ Ja sarakstā nav nevienas atbilstošas grāmatas, __atgriež__  null, lai norādītu, ka tāda grāmata nav atrasta.  

4. __list__ – Parādīt visas grāmatas  
    _Kā tas darbojas:_  
    - Ielādē grāmatas no books.csv.  
    - Izvada tās konsolē.  
    __Ka ir realizets koda:__  
    ```  
    // Grāmatu lejupielāde no CSV
    public static List<Book> loadBooksFromCSV() {
        List<Book> books = new ArrayList<>();
        try (BufferedReader reader = Helper.getReader(BOOKS_FILE)) {
        String line;
        while ((line = reader.readLine()) != null) {
            String[] data = line.split(","); // Sadaliet virkni ar komatu
            if (data.length >= 4) { // Parbauda datus
                try {
                    // Pārvērtiet gadu par skaitli
                    int year = Integer.parseInt(data[2]);
                    // Pārvērtiet grāmatas statusu uz Būla vērtību
                    boolean isBorrowed = Boolean.parseBoolean(data[3]);

                    // Grāmatas izveide
                    Book book = new Book(data[0], data[1], year);
                    if (isBorrowed) {
                        book.borrowBook(); // Ja grāmata ir paņemta, tad atzīmējam kā paņemtu
                    }
                    books.add(book); // Pievienojiet grāmatu sarakstam
                } catch (NumberFormatException e) {
                    System.out.println("Data conversion error: " + line);
                }
            } else {
                System.out.println("Invalid string format: " + line);
            }
        }
        } catch (IOException e) {
           System.out.println("Error loading books from CSV.");
        }
        return books;
    }  
    ```  
    __Loģika:__  
     - __Nolasa CSV failu rindu pa rindai__ un sadala to pa komatiem.  
     - __Pārbauda, vai dati ir pilnīgi,__ un konvertē gadu (int) un aizņemšanas statusu (boolean).  
     - __Izveido Book objektu__ un, ja nepieciešams, atzīmē to kā aizņemtu.  
     - __Ja dati nederīgi__, izvada kļūdas ziņojumu, beigās atgriež sarakstu ar grāmatām.  

5. __list_client__ – Parādīt visus klientus  
    _Ka tas darbojas_  
    - Izvada visus reģistrētos klientus tabula formata.  
    __Ka ir realizets koda:__  
    ```  
    public static void listClients(List<Client> clients) {
        TableFormatter.printClientsTable(clients);
    }  

    public static void printClientsTable(List<Client> clients) {
     // Inicializējiet klienta un grāmatu maksimālos garumus
     int maxClientNameLength = "Client Name".length();
     int maxBooksLength = "Borrowed Books".length();

     // Atrodiet klienta vārda maksimālo garumu un grāmatu sarakstu
     for (Client client : clients) {
        maxClientNameLength = Math.max(maxClientNameLength, client.getName().length());

        // Veidojot līniju ar grāmatu nosaukumiem
        String borrowedBooksList = client.getBorrowedBook().stream()
            .map(Book::getTitle)
            .collect(Collectors.joining(", "));

        maxBooksLength = Math.max(maxBooksLength, borrowedBooksList.length());
    }

    // Formāts tabulas attēlošanai
    String headerFormat = "| %-"+maxClientNameLength+"s | %-"+maxBooksLength+"s |";
    String separator = "-".repeat(maxClientNameLength + maxBooksLength + 7);

    // Tabulas drukāšana
    System.out.println(HEADER_COLOR + separator + RESET);
    System.out.printf(HEADER_COLOR + headerFormat + "%n", "Client Name", "Borrowed Books");
    System.out.println(HEADER_COLOR + separator + RESET);

    // Mēs šķirojam klientus un parādām viņu datus
    for (Client client : clients) {
        String borrowedBooksList = client.getBorrowedBook().stream()
            .map(Book::getTitle)
            .collect(Collectors.joining(", "));

        System.out.printf(ROW_COLOR + headerFormat + "%n" + RESET, client.getName(), borrowedBooksList.isEmpty() ? "None" : borrowedBooksList);
    }

     System.out.println(HEADER_COLOR + separator + RESET);
    }
    
    ```  
    __Loģika:__  
   1. __Tabulas formatēšana::__  
       - Inicializējam maksimālos garumus klienta vārdiem un aizņemto grāmatu sarakstiem: maxClientNameLength un maxBooksLength.  
   2. __Maksimālā garuma aprēķins:__  
       - Ar ciklu pārbaudām katra klienta vārdu garumu un atjaunojam maxClientNameLength.  
       - Pārbaudām katra klienta aizņemto grāmatu sarakstu un atjaunojam maxBooksLength, lai nodrošinātu, ka viss teksts labi ietilpst tabulā.  
   3. __Tabulas formāta izveide:__  
        - Izveidojam formātu galvenajai tabulai (headerFormat), ņemot vērā maksimālos garumus.  
        - Izveidojam atdalītāju (separator), kas veido līniju no - simboliem, kas atbilst tabulas platumam.  
   4. __Tabulas izdrukāšana:__  
        - Izdrukājam galveni, izmantojot aprēķināto formātu.  
        - Izdrukājam atdalītāju.  
   5. __Klientu datu izvadīšana:__  
        - Klientus šķirojam un izvadām katra klienta vārdu un aizņemto grāmatu sarakstu tabulā.   
        - Ja klientam nav aizņemtu grāmatu, tiek parādīts "None".  
        - Tabulas beigās izdrukājam atdalītāju.



6. __register__ – Reģistrēt jaunu klientu  
    _Kā tas darbojas:_  
    - Pieprasa lietotājam ievadīt klienta vārdu
    - Reģistrē jaunu klientu CSV faila.  
    __Ka ir realizets koda:__  
    ```  
    private static void registerClient(Scanner scanner, List<Client> clients) {
        System.out.print("Enter client name: ");
        String name = scanner.nextLine();
        Client.registerClient(clients, name);
        CSVHandler.saveClientsToCSV(clients);
    }
    ```  
    __Loģika:__  
   1. __Klienta vārda ievade:__  
       - Programma pieprasa lietotājam ievadīt klienta vārdu ar System.out.print("Enter client name: ").  
       - Vārds tiek saglabāts mainīgajā name ar scanner.nextLine().
   2. __Klienta reģistrēšana:__  
        - Izsauc Client.registerClient(clients, name) metodi, lai pievienotu jauno klientu sarakstam clients, izmantojot ievadīto vārdu.
   3. __Datu saglabāšana CSV failā:__  
        - Izsauc CSVHandler.saveClientsToCSV(clients), lai saglabātu atjaunināto klientu sarakstu CSV failā.
    

7. __count__ – Skaitīt grāmatas  
    _Kā tas darbojas:_  
    - Izvada kopējo grāmatu skaitu sistēmā.  
    __Ka ir realizets koda__  
    ```  
    public static void countBorrowedBooks(List<Book> books) {
        long count = books.stream().filter(Book::isBorrowed).count();
        System.out.println(ColorScheme.TABLE_HEADER_COLOR + " Borrowed books: " + count + ColorScheme.TABLE_RESET);
    }
    ```  
    __Loģika__  
   1. __Filtrē grāmatas:__  
        - Programma izmanto books.stream() lai izveidotu straumi no grāmatām.  
        - Tiek izmantota filter(Book::isBorrowed), lai atlasītu tikai tās grāmatas, kuru status ir "aizņemta" (t.i., isBorrowed() atgriež true).
   2. __Skaitīšana:__  
        - Ar count() tiek saskaitītas grāmatas, kas atbilst filtrēšanas kritērijiem (tās, kas ir aizņemtas).  
   3. __Izvade:__  
        - Pēc tam tiek izdrukāts aizņemtās grāmatas skaits ar krāsu iestatījumiem no ColorScheme.TABLE_HEADER_COLOR un ColorScheme.TABLE_RESET.  

8. __sort_asc__ – Kārtot grāmatas pēc gada (pieaugošā secībā)  
    _Kā tas darbojas:_  
    - Kārto grāmatas sarakstu pēc izdošanas gada pieaugošā secībā.  
    __Ka ir realizets koda__  
    ```  
    public static void sortBooksByYearAsc(List<Book> books) {
        books.sort(Book.sortByYearAsc);
        listBooks(books);
    }
    ```  
    __Loģika__  
   1. __Kārtot grāmatas:__  
        - Programma izmanto books.sort(Book.sortByYearAsc) metodi, kas kārtos grāmatas sarakstā pēc to izdošanas gada augošā secībā. 
        - Book.sortByYearAsc ir salīdzināšanas funkcija, kas salīdzina grāmatas pēc gada, nodrošinot, ka vecākās grāmatas būs saraksta sākumā.
   2. __Grāmatu parādīšana:__  
        - Pēc grāmatu kārtošanas tiek izsaukta listBooks(books) metode, lai izvadītu sarakstu ar kārtotajām grāmatām uz ekrāna.

9. __sort_desc__ – Kārtot grāmatas pēc gada (dilstošā secībā)  
    _Kā tas darbojas:_  
    - Kārto grāmatas sarakstu pēc izdošanas gada dilstošā secībā.  
    __Ka ir realizets koda__  
    ```  
    public static void sortBooksByYearDesc(List<Book> books) {
        books.sort(Book.sortByYearDesc);
        listBooks(books);
    }

    ```  
    __Loģika__  
   1. __Kārtot grāmatas:__  
        - Programma izmanto books.sort(Book.sortByYearDesc) metodi, kas kārtos grāmatas sarakstā pēc to izdošanas gada dilstošā secībā.
        - Book.sortByYearDesc ir salīdzināšanas funkcija, kas salīdzina grāmatas pēc gada, nodrošinot, ka jaunākās grāmatas būs saraksta sākumā.
   2. __Grāmatu parādīšana:__  
        - Pēc grāmatu kārtošanas, tiek izsaukta listBooks(books) metode, lai izvadītu sarakstu ar kārtotajām grāmatām uz ekrāna.

10. __borrow__ – Paņemt grāmatu no bibliotekas   
    _Kā tas darbojas:_  
    - Pieprasa lietotājam ievadīt grāmatas nosaukumu, kuru viņš vēlas aizņemt.  
    - Atzīmē grāmatu kā aizņemtu.  
    __Kaz tas ir realizets koda__  
    ```  
    private static void borrowBook(Scanner scanner, List<Book> books,   List<Client> clients) {
        System.out.print("Enter client name: ");
        String clientName = scanner.nextLine();
        Client client = Client.findClientByName(clients, clientName);
        if (client == null) {
            System.out.println("Client not found.");
            return;
        }

        System.out.print("Enter book title to borrow: ");
        String bookTitle = scanner.nextLine();
        Book book = Book.findBookByTitle(books, bookTitle);
        if (book != null) {
            client.borrowBook(book);
            CSVHandler.saveBorrowedBook(clientName, bookTitle);
        } else {
            System.out.println("Book not found.");
        }
    }
    ```  
    __Loģika__  
   1. __Klienta meklēšana:__  
        - Programma pieprasa lietotājam ievadīt klienta vārdu.  
        - Tiek meklēts klients ar šo vārdu sarakstā, izmantojot Client.findClientByName metodi. Ja klients nav atrasts, tiek izvadīts ziņojums "Client not found." un metode tiek pārtraukta.
   2. __Grāmatas meklēšana:__  
        - Pēc tam, kad klients ir atrasts, programma pieprasa ievadīt grāmatas nosaukumu.  
        - Grāmata tiek meklēta sarakstā, izmantojot Book.findBookByTitle metodi. Ja grāmata netiek atrasta, tiek izvadīts ziņojums "Book not found." un metode tiek pārtraukta.
   3. __Grāmatas aizņemšana:__  
        - Ja gan klients, gan grāmata ir atrasti, programma atzīmē grāmatu kā aizņemtu, izmantojot klienta metodi borrowBook, un veic atjauninājumus:  
        - Grāmatas aizņemšanas informācija tiek saglabāta ar CSVHandler.saveBorrowedBook.
    

11. __return__ – Atgriezt aizņemto grāmatu  
    _Kā tas darbojas:_  
    - Pieprasa ievadit klientu vardu kurš aizņem gramatu un grīb atgriezt gramatu biblioteka
    - Pieprasa lietotājam ievadīt grāmatas nosaukumu, kuru viņš vēlas atgriezt.  
    - Atzīmē grāmatu kā pieejamu.  
    __Ka ir realizets koda__  
    ```  
    private static void returnBook(Scanner scanner, List<Book> books, List<Client> clients) {
        System.out.print("Enter client name: ");
        String clientName = scanner.nextLine();
        Client client = Client.findClientByName(clients, clientName);
        if (client == null) {
            System.out.println("Client not found.");
            return;
        }

        System.out.print("Enter book title to return: ");
        String bookTitle = scanner.nextLine();
        Book book = Book.findBookByTitle(books, bookTitle);
        if (book != null) {
            client.returnBook(book);
            CSVHandler.saveClientsToCSV(clients);
            CSVHandler.removeBorrowedBook(clientName, bookTitle);
        } else {
            System.out.println("Book not found.");
        }
    }
    ```  
    __Loģika__  
   1. __Klienta meklēšana:__  
        - Pieprasa ievadīt klienta vārdu.  
        - Meklē klientu sarakstā, izmantojot metodi Client.findClientByName.  
        - Ja klients netiek atrasts, izvada ziņojumu "Client not found." un iznāk no metodes.
   2. __Grāmatas meklēšana:__  
        - Pieprasa ievadīt grāmatas nosaukumu.  
        - Meklē grāmatu sarakstā, izmantojot metodi Book.findBookByTitle.  
        - Ja grāmata netiek atrasta, izvada ziņojumu "Book not found."  
   3. __Grāmatas atgriešana:__  
        - Ja gan klients, gan grāmata ir atrasti, tiek izsaukta klienta metode returnBook, kas veic grāmatas atgriešanu.  
        - Saglabā atjaunoto klientu datus CSV failā, izmantojot CSVHandler.saveClientsToCSV.  
        - Noņem aizņemto grāmatu no klienta saraksta, izmantojot CSVHandler.removeBorrowedBook.
    
12. __exit – Iziet no programmas__  
    _Kā tas darbojas:_  
    - Iziet no programmas.  
    __Ka tas ir realizets koda__  
    ```  
    case "exit":
        System.out.println("Goodbye!");
        System.exit(0);
        break;  
    ```  
    __Loģika__  
   1. __Ziņojuma izvadīšana:__  
        - Pirms iziešanas no programmas tiek izvadīts ziņojums __"Goodbye!"__ konsolē, lai lietotājs zinātu, ka programma beigs savu darbību.  
   2. __Programmas beigšana:__  
        - Izmanto __System.exit(0)__ komandu, lai nekavējoties izbeigtu programmas izpildi. Parametrs __0__ norāda, ka programma beidzas veiksmīgi (nav kļūdu).  




