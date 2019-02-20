# Kastelic Java
## Povezovanje na SQL SUPB

Prenesi **MySQL connector** iz https://dev.mysql.com/downloads/connector/j/5.1.html. Dobiti moras datoteko `mysql-connector-java-8.0.14.jar`.

Uvozi dobljeno datoteko v **Eclipse**: Pojdi v `Project > Properties` in tam `Java Build Path > Libraries > Add External JARs...` nato izberi preneseno datoteko.

Uvozi dobljeno datoteko v **NetBeans**: Pojdi v `Project > Properties` in tam `Libraries > Compile` in klikni `Ass JAR/Folder` nato izberi preneseno datoteko.

Uvozi knjižnico za povezovanje z SQL:

	import java.sql.*;

#### Povezava na bazo in poizvedba
Ustvari povezavo na bazo in izvedi query:

	Connection conn = DriverManager.getConnection("jdbc:mysql://localhost/<ime baze>?serverTimezone=GMT&user=root&password=");
	Statement stmt = conn.createStatement();
	if(stmt.execute("SELECT * FROM uporabniki")) {
		ResultSet rs = stmt.getResultSet();
	}

Povezava z bazo se shrani v objekt `Connection` (dokumentacija: https://docs.oracle.com/javase/7/docs/api/java/sql/Connection.html).

Iz povezave lahko naredimo `Statment` (dokumentacija: https://docs.oracle.com/javase/7/docs/api/java/sql/Statement.html), ki nam omogoča izvajanje SQL poizvedb.

`Statment` ima metode:
* `excecute(String query)` - izvede SQL poizvedbo
* `getResult` - vrne rezultat SQL poizvedbe v obliki `ResultSet`

> Zamenjaj **ime baze** z pravim imenom!

> Navedena kodal lahko vrže `SQLException`!

#### Ogled rezultata
V tem primeru je `rs` objekt razreda `ResultSet` (dokumentacija: https://docs.oracle.com/javase/7/docs/api/java/sql/ResultSet.html)

	while(rs.next()) {
		System.out.println(rs.getString(1) + " " + rs.getString(2) + " " + rs.getString(3) + " " + rs.getString(4));
	}

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

## Datoteke
