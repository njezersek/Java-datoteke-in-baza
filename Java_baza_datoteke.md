# Java
## Povezovanje na SQL SUPB

Prenesi **MySQL connector** iz https://dev.mysql.com/downloads/connector/j/5.1.html. Dobiti moras datoteko `mysql-connector-java-8.0.14.jar`.

Uvozi dobljeno datoteko v **Eclipse**: Pojdi v `Project > Properties` in tam `Java Build Path > Libraries > Add External JARs...` nato izberi preneseno datoteko.

Uvozi dobljeno datoteko v **NetBeans**: Pojdi v `Project > Properties` in tam `Libraries > Compile` in klikni `Ass JAR/Folder` nato izberi preneseno datoteko.

Uvozi knjižnico za povezovanje z SQL:
```java
import java.sql.*;
```
#### Povezava na bazo in poizvedba
Ustvari povezavo na bazo in izvedi query:
```java
Connection conn = DriverManager.getConnection("jdbc:mysql://localhost/<ime baze>?serverTimezone=GMT&user=root&password=");
Statement stmt = conn.createStatement();
if(stmt.execute("SELECT * FROM uporabniki")) {
	ResultSet rs = stmt.getResultSet();
}
```
Povezava z bazo se shrani v objekt `Connection` (dokumentacija: https://docs.oracle.com/javase/7/docs/api/java/sql/Connection.html).

Iz povezave lahko naredimo `Statment` (dokumentacija: https://docs.oracle.com/javase/7/docs/api/java/sql/Statement.html), ki nam omogoča izvajanje SQL poizvedb.

`Statment` ima metode:
* `excecute(String query)` - izvede SQL poizvedbo
* `getResult` - vrne rezultat SQL poizvedbe v obliki `ResultSet`

> Zamenjaj **ime baze** z pravim imenom!

> Navedena kodal lahko vrže `SQLException`!

#### Ogled rezultata
V tem primeru je `rs` objekt razreda `ResultSet` (dokumentacija: https://docs.oracle.com/javase/7/docs/api/java/sql/ResultSet.html)
```java
while(rs.next()) {
	System.out.println(rs.getString(1) + " " + rs.getString(2) + " " + rs.getString(3) + " " + rs.getString(4));
}
```
`ResultSet` ima metode:
* `next()` - pojdi na naslednjo vrstivo v tebeli
* `getString(int stolpec)` - vrednost stolpca (stejejo se od 1 naprej)
* `getInt(int stolpec)`
* `getFloat(int stolpec)`
* `getString(String imeStolpca)` - naredi isto kot zgornji primeri, le da za parameter vzame ime stolpca
* `getMetaData()` - vrne meta podatke o tabeli tipa `ResultSetMetaData`

#### Meta podatki o tabeli
Meta odatke o tabeli lahko dobimo iz `ResultSet`.
```java
ResultSetMetaData meta = rs.getMetaData();
for(int i=1; i<=meta.getColumnCount(); i++) {
	System.out.print(meta.getColumnName(i) + " ");
}
System.out.println();
```
`ResultSetMetaData` ima metode:
* `getColumnCount` - vrne število stolpcev v tabeli
* `getColumnName` - vrne ime stolpca v tabeli

#### Program za izpis tabele
Program izpiše imena stolpcev in njihovo vsebino

```java
import java.sql.*;
public class SQLconnection {
	public static void main(String[] args) throws SQLException {
		Connection conn = DriverManager.getConnection("jdbc:mysql://localhost/test?serverTimezone=GMT&user=root&password=");
		Statement stmt = conn.createStatement();
		if(stmt.execute("SELECT * FROM uporabniki")) {
			ResultSet rs = stmt.getResultSet();

			ResultSetMetaData meta = rs.getMetaData();
			for(int i=1; i<=meta.getColumnCount(); i++) {
				System.out.print(meta.getColumnName(i) + " ");
			}
			System.out.println();
			while(rs.next()) {
				for(int i=1; i<=meta.getColumnCount(); i++) {
					System.out.print(rs.getString(i) + " ");
				}
				System.out.println();
			}
		}
	}
}
```

#### Vstavljanje v bazo
Za vstavljanje v bazo uporabi tak SQL stavek:
```SQL
INSERT INTO `uporabniki` (`id`, `username`, `password`, `email`)
VALUES (NULL, 'ana', 'gugalnica345', 'ana.lanina@yahoo.com');
```
Cel program:
```java
import java.sql.*;
public class SQLconnection {
	public static void main(String[] args) throws SQLException {
		Connection conn = DriverManager.getConnection("jdbc:mysql://localhost/test?serverTimezone=GMT&user=root&password=");
		Statement stmt = conn.createStatement();
		stmt.execute("INSERT INTO `uporabniki` (`id`, `username`, `password`, `email`) VALUES (NULL, 'ana', 'gugalnica345', 'ana.lanina@yahoo.com');");
	}
}
```

## Besdilne datoteke
Za delo z datotekami potrebujemo
```java
import java.io.*;
```
### Pisanje v datoteko
> Navedena koda lahko vrže `IOException`!

#### FileOutputStream
`FileOutputStream` lahko v datotekoa zapiše samo en byte naenkrat in inma nobenih posebnih metod. Njegov konstruktor ima dva parametra. Prvi pove ime datoteke, drugi pa ali naj se datoteka prepiše ali dopiše.
```java
FileOutputStream fs = new FileOutputStream("test.txt", true);
fs.write(65);
fs.write('B');
fs.close();
```

#### BufferedOutputStream
Iz `FileOutputStream` lahko naredimom `BufferedOutputStream`, ki ne omogoča nič novega samo med datoteko postavi buffer.
```java
FileOutputStream fs = new FileOutputStream("test.txt", true);
BufferedOutputStream bs = new BufferedOutputStream(fs);
bs.write(65);
bs.write('B');
bs.close();
```

#### FileWriter
Konstruktor za `FileWriter` ima dva parametra. Prvi pove pot do datoteke, drugi pa ali naj se datoteka prešiše ali dopiše.
```java
FileWriter fw = new FileWriter("test.txt", true);
fw.write("Hello World\n");
fw.write("Nejc Jezeršek\n");
fw.close();
```

#### PrintWriter
Iz `BufferedOutputStream` ali pa samo `FileOutputStream` ali pa `FileWriter` lahko naredimo `PrintWriter`, ki omogoča pisanje podobno kot `System.out`.
```java
FileOutputStream fs = new FileOutputStream("test.txt", true);
BufferedOutputStream bs = new BufferedOutputStream(fs);
PrintWriter pw = new PrintWriter(bs);
pw.write("123 ");
pw.println("To je test");
pw.close();
```
```java
FileWriter fw = new FileWriter("test.txt", true);
PrintWriter pw = new PrintWriter(fw);
```

### Branje iz datoteke
> Navedena koda lahko vrže `FileNotFoundException` in `IOException`!

#### FileInputStream in BufferedInputStream
```java
FileInputStream fs = new FileInputStream("test.txt");
BufferedInputStream bs = new BufferedInputStream(fs);
```
V konstruktorju `FileInputStream` podamo ime datoteke. Iz `FileInputStream` lahko naredimo `BufferedInputStream`. Z njim lahko beremo posamezne byte iz datoteke.

#### FileReader in BufferedReader
Najlažji način za branje datotek je z `BufferedReader`.
```java
FileReader fr = new FileReader("test.txt");
BufferedReader bs = new BufferedReader(fr);
String line;
while((line = bs.readLine()) != null) {
	System.out.println(line);
}
bs.close();
```
`BufferedReader` ima metodo `readLine()`, ki vrne vrstico datoteke. Ko zmanjka vrstic vrne `null`.

## Binarne datoteke
> Navedena koda lahko vrže `IOException`!
#### DataOutputStream/DataInputStream
```java
FileOutputStream fos = new FileOutputStream("test.bin");
DataOutputStream dos = new DataOutputStream(fos);
...
dos.close();
```
`DataOutputStream` nam omogoča pisanje primitivov v datoteko. Naprimer:
```java
dos.writeBoolean(true);
dos.writeInt(15);
dos.writeChars("hello");
dos.writeFloat(9.6f);
dos.writeDouble(4.1);
```

Če želimo to datoteko kasneje prebreti uporabimo `DataInputStream`.

```java
FileInputStream fis = new FileInputStream("test.bin");
DataInputStream dis = new DataInputStream(fis);

boolean b1 = dis.readBoolean(); // true
int i1 = dis.readInt();	// 15
char c1 = dis.readChar(); // 'h'
dis.skip(6); // 3 * 2b
char c2 = dis.readChar(); // 'o'
float f1 = dis.readFloat(); // 9.6
double d1 = dis.readDouble(); // 4.1
```

> Žal ne obstaja metoda `readChars()`, zato je treba vsak znak v nizu prebreti posebej. Lahko pa tudi preskočimo kakšen znak z `skip()` metodo.

#### ObjectOutputStream/ObjectInputStream (Serializacija)
> Navedena koda lahko vrže `IOException` ali `ClassNotFoundException`

Serializacija nam omogoča da objekt zapišemo v datoteko, ki jo lahko kasneje preberemo in dobimo bojekt nazaj. Potrebujemo `ObjectOutputStream` (dokumentacija: https://docs.oracle.com/javase/7/docs/api/java/io/ObjectOutputStream.html), ki ga naredimo iz `FileOutputStream`.

```java
FileOutputStream fos = new FileOutputStream("test.bin");
ObjectOutputStream oos = new ObjectOutputStream(fos);
String test = new String("test");
oos.writeObject(test);
oos.close();
```

Če želimo na ta način shraniti lastnen razrede morajo le ti implementirati `Serializable`. Poleg tega pa morajo tudi vse lastnosti (spremenljvke) razreda implementirati `Serializable`. Če neke lastnosti ne želimo vkljušiti v serializacijo lahko pred njo dodamo `transient`.

```java
import java.io.Serializable;
public class Oseba implements Serializable{
	public int starost;
	public String ime;
	public String priimek;
	public transient int id;

	Oseba(String ime, String priimek, int starost){
		this.ime = ime;
		this.priimek = priimek;
		this.starost = starost;
	}
}
```
Potem lahko objekt tega razreda shranimo v datoteko:

```java
FileOutputStream fos = new FileOutputStream("test.ser");
ObjectOutputStream oos = new ObjectOutputStream(fos);
Oseba test = new Oseba("Janez", "Novak", 21);
oos.writeObject(test);
oos.close();
```
V datoteko se shrani tole:
```
¬í�sr�OsebaÍc	TL1ˆ�I�starostL�imet�Ljava/lang/String;L�priimekq�~�xp���t�Janezt�Novak
```

Objekte, ki smo jih shranili v datoteko z `ObjectOutputStream` lahko z `ObejectInputStream` pretvorimo nazaj v objekte.

Da lahko objekt preberemo moramo seveda imeti definiran isti razred, ki smo ga uporabili za kreacijo objekta.

V isto datoteko lahko shranimo več različnih vrst objektov, le paziti moramo da jih preberemo v pravem vrstnem redu:
```java
// pisi v datoteko
FileOutputStream fos = new FileOutputStream("test.ser");
ObjectOutputStream oos = new ObjectOutputStream(fos);
Oseba o1 = new Oseba("Janez", "Novak", 21);
Oseba o2 = new Oseba("Ana", "Preseren", 17);
oos.writeObject("to je test");
oos.writeObject(o1);
oos.writeObject(o2);
oos.close();

// preberi datoteko
FileInputStream fis = new FileInputStream("test.ser");
ObjectInputStream ois = new ObjectInputStream(fis);
String s = (String) ois.readObject();
Oseba oseba1 = (Oseba) ois.readObject();
Oseba oseba2 = (Oseba) ois.readObject();
System.out.println(s); // "to je test"
System.out.println(oseba1.ime); // "Janez"
System.out.println(oseba2.ime); // "Ana"
```

#### RandomAccesFile
`RandomAccesFile` (dokumentacija: https://docs.oracle.com/javase/7/docs/api/java/io/RandomAccessFile.html) omogoča branje in pisanje hkrati.

```java
RandomAccessFile ra = new RandomAccessFile("test.txt", "rw");
ra.writeChars("to je test");
ra.seek(6);
ra.readChar(); // 'j'
ra.close();
```

V konstruktor podamo **ime datoteke** in **način** (branje in pisanje).
V datoteko lahko pišemo podobno kot z `DataOutputStream` in beremo kot z `DataInputStream`.

Posebnost `RandomAccesFile` je metoda `seek(int n)`, ki omogoča da datotečni kazalec prestavimo na želeno mesto (absolutno od začetka datoteke).


## Upravljanje datotek

#### Ustvarjanje nove datoteke

```java
File f = new File("datoteka.txt");
f.createNewFile();
```

#### Preimenovanje datoteke

```java
File f = new File("datoteka.txt");
f.renameTo(new File("preimenovan.txt"));
```

#### Izbris datoteke

```java
File f = new File("datoteka.txt");
f.delete();
```

#### Preveri ali datoteka obstaja
```java
File f = new File("datoteka.txt");
f.exists(); // true/false
```
