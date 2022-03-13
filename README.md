# jdbc-mysql
jdbc mysql
To start with interfacing Java Servlet Program with JDBC Connection:

Proper JDBC Environment should set-up along with database creation.
To do so, download the mysql-connector.jar file from the internet,
As it is downloaded, move the jar file to the apache-tomcat server folder,
Place the file in lib folder present in the apache-tomcat directory.
To start with the basic concept of interfacing:
Step 1: Creation of Database and Table in MySQL
As soon as jar file is placed in the folder, create a database and table in MySQL,

mysql> create database demoprj;
Query OK, 1 row affected (4.10 sec)

mysql> use demoprj
Database changed

mysql> create table demo(id int(10), string varchar(20));
Query OK, 0 rows affected (1.93 sec)

mysql> desc demo;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| id     | int(10)     | YES  |     | NULL    |       |
| string | varchar(20) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
2 rows in set (0.40 sec)
Step 2: Implementation of required Web-pages
Create a form in HTML file, where take all the inputs required to insert data into the database. Specify the servlet name in it, with the POST method as security is important aspects in database connectivity.
<!DOCTYPE html>
<html>
<head>
<title>Insert Data</title>
</head>
<body>
    <!-- Give Servlet reference to the form as an instances 
    GET and POST services can be according to the problem statement-->
    <form action="./InsertData" method="post">
        <p>ID:</p> 
        <!-- Create an element with mandatory name attribute,
        so that data can be transfer to the servlet using getParameter() -->
        <input type="text" name="id"/>
        <br/>
        <p>String:</p> 
        <input type="text" name="string"/>
        <br/><br/><br/>
        <input type="submit"/>
    </form>
</body>
</html>
Output:
output

Submit the data (with validation) as all the required data are inserted.

Step 3: Creation of Java Servlet program with JDBC Connection
To create a JDBC Connection steps are

Import all the packages
Register the JDBC Driver
Open a connection
Execute the query, and retrieve the result
Clean up the JDBC Environment
Create a separate class to create a connection of database, as it is a lame process to writing the same code snippet in all the program. Create a .java file which returns a Connection object.

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
  
// This class can be used to initialize the database connection
public class DatabaseConnection {
    protected static Connection initializeDatabase()
        throws SQLException, ClassNotFoundException
    {
        // Initialize all the information regarding
        // Database Connection
        String dbDriver = "com.mysql.jdbc.Driver";
        String dbURL = "jdbc:mysql:// localhost:3306/";
        // Database name to access
        String dbName = "demoprj";
        String dbUsername = "root";
        String dbPassword = "root";
  
        Class.forName(dbDriver);
        Connection con = DriverManager.getConnection(dbURL + dbName,
                                                     dbUsername, 
                                                     dbPassword);
        return con;
    }
}
Step 4: To use this class method, create an object in Java Servlet program
Below program shows Servlet Class which create a connection and insert the data in the demo table,

import java.io.IOException;
import java.io.PrintWriter;
import java.sql.Connection;
import java.sql.PreparedStatement;
  
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
  
// Import Database Connection Class file
import code.DatabaseConnection;
  
// Servlet Name
@WebServlet("/InsertData")
public class InsertData extends HttpServlet {
    private static final long serialVersionUID = 1L;
  
    protected void doPost(HttpServletRequest request, 
HttpServletResponse response)
        throws ServletException, IOException
    {
        try {
  
            // Initialize the database
            Connection con = DatabaseConnection.initializeDatabase();
  
            // Create a SQL query to insert data into demo table
            // demo table consists of two columns, so two '?' is used
            PreparedStatement st = con
                   .prepareStatement("insert into demo values(?, ?)");
  
            // For the first parameter,
            // get the data using request object
            // sets the data to st pointer
            st.setInt(1, Integer.valueOf(request.getParameter("id")));
  
            // Same for second parameter
            st.setString(2, request.getParameter("string"));
  
            // Execute the insert command using executeUpdate()
            // to make changes in database
            st.executeUpdate();
  
            // Close all the connections
            st.close();
            con.close();
  
            // Get a writer pointer 
            // to display the successful result
            PrintWriter out = response.getWriter();
            out.println("<html><body><b>Successfully Inserted"
                        + "</b></body></html>");
        }
        catch (Exception e) {
            e.printStackTrace();
        }
    }
}
Step 5: Get the data from the HTML file
To get the data from the HTML file, the request object is used which calls getParameter() Method to fetch the data from the channel. After successful insertion, the writer object is created to display a success message.

After insertion operation from Servlet, data will be reflected in MySQL Database

Output:
success

Result in MySQL Interface

mysql> select * from demo;
+------+---------------+
| id   | string        |
+------+---------------+
|    1 | GeeksForGeeks |
+------+---------------+
1 row in set (0.06 sec)
