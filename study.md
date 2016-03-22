# test

package miscellaneous;

public class DuplicateEntryException extends Exception {

	public DuplicateEntryException(String message) {
		super(message);
		
	}
}


///////////////////////////////////////////////////////////

package package_main;

import java.sql.SQLException;

import package_service.EmployeeDetail;
import package_services.EmployeeService;

public class EmployeeMain {



	public static void main(String args[]) throws Exception {
		EmployeeService src=new EmployeeService();
		EmployeeDetail e1=new EmployeeDetail("james","bond","50");

		EmployeeDetail e2=new EmployeeDetail("jones","indiana","66");
		
		EmployeeDetail e3=new EmployeeDetail("27","asdasdsa","jangari","60");

		try {
			src.addition(e1);
			src.addition(e2);
			src.updateage(e3);
			
			//System.out.println(src.getEmp(43));
			src.getEmp(-1);
		}


		catch(SQLException e) {
			System.out.println("duplicate entry");
			e.printStackTrace();
		}
		//src.updateage(c1);
		


	}


}
/////////////////////////////////////////////////////////////////////////////////
package package_repo;

import java.io.PrintStream;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.Statement;

import miscellaneous.DuplicateEntryException;

import package_service.EmployeeDetail;


public class EmployeeRepo {

	public void add(EmployeeDetail e) throws Exception
	{

		Class.forName("com.mysql.jdbc.Driver");  

		Connection con=(Connection) DriverManager.getConnection(  
				"jdbc:mysql://localhost:3306/test","root","kishore007");  

		PreparedStatement preparedStatement;
		//Statement sat=(Statement)con.createStatement();
		String sql1 = "INSERT INTO employees VALUES (?,?,?,?)";
		preparedStatement = (PreparedStatement) con.prepareStatement(sql1);

		preparedStatement.setString(1,e.getId());
		preparedStatement.setString(2,e.getFirst_name());
		preparedStatement.setString(3,e.getLast_name());
		preparedStatement.setString(4,e.getAge());

		//validating for duplicate record

		try {
			ResultSet rs=preparedStatement.executeQuery("select first_name, last_name from employees");
			while(rs.next()) {

				String fn=rs.getString("first_name");
				String ln=rs.getString("last_name");
				//System.out.println(fn);

				if(e.getFirst_name().equals(fn)&&e.getLast_name().equals(ln)) {
					throw new DuplicateEntryException("Danger");
				} 
			}
			// execute insert SQL statement
			preparedStatement.executeUpdate();
		} 

		catch(DuplicateEntryException d){
			System.err.println("x-x-x-x-x DUPLICATE ENTRY x-x-x-x-x");
		}

		con.close();

	}
	public void update(EmployeeDetail e) throws Exception{
		Class.forName("com.mysql.jdbc.Driver");  

		Connection con=(Connection) DriverManager.getConnection(  
				"jdbc:mysql://localhost:3306/test","root","kishore007");  

		PreparedStatement preparedStatement;

		String sql = "UPDATE employees set first_name=?,last_name=? where id=?";
		preparedStatement = (PreparedStatement) con.prepareStatement(sql);

		preparedStatement.setString(1,e.getFirst_name());
		preparedStatement.setString(2,e.getLast_name());
		preparedStatement.setString(3,e.getId());

		// execute insert SQL statement
		preparedStatement.executeUpdate();

		ResultSet rs=preparedStatement.executeQuery("select first_name from employees");

		while(rs.next())  
		{
			String fn=rs.getString("first_name");
			System.out.println(fn);
		}
		con.close();
	}

	public String getEmployee(int age) throws Exception{
		Class.forName("com.mysql.jdbc.Driver");  

		Connection con=(Connection) DriverManager.getConnection(  
				"jdbc:mysql://localhost:3306/test","root","kishore007");  

		Statement stmt = con.createStatement();

		if(18<age && age<60){
			ResultSet rs=stmt.executeQuery("select * from employees where age="+age+"");

			while(rs.next())  
			{

				String id=rs.getString("id");
				String fn=rs.getString("first_name");
				String ln=rs.getString("last_name");
				String a=rs.getString("age");
				System.out.println("| ID: "+id+" |-| First Name: "+fn+" |-| Last Name: "+ln+" |-| Age: "+a+" |");
				return fn;

			}

		}else{
			//System.err.println("REy Error");
			String e = "Incorrect age";
			return e;
		}
		con.close();
		return null;

	}


}



////////////////////////////////////////////////////////////////////////////////////////////
package package_service;

public class EmployeeDetail {




	public EmployeeDetail(String first_name, String last_name, String age) {
		super();
		this.first_name = first_name;
		this.last_name = last_name;
		this.age = age;
		//this.id = id;
	}
	
	public EmployeeDetail(String id, String first_name, String last_name, String age) {
		super();
		this.first_name = first_name;
		this.last_name = last_name;
		this.age = age;
		this.id = id;
	}

	private String first_name;
	private String last_name;
	private String age;
	private String id;





	public String getFirst_name() {
		return first_name;
	}
	public void setFirst_name(String first_name) {
		this.first_name = first_name;
	}
	public String getId() {
		return id;
	}
	public void setId(String id) {
		this.id = id;
	}

	public String getLast_name() {
		return last_name;
	}
	public void setLast_name(String last_name) {
		this.last_name = last_name;
	}
	public String getAge() {
		return age;
	}
	public void setAge(String age) {
		this.age = age;
	}
}
////////////////////////////////////////////////////////////////////////


package package_services;

import package_repo.EmployeeRepo;
import package_service.EmployeeDetail;


public class EmployeeService {
	private EmployeeRepo repo=new EmployeeRepo();

	public void addition(EmployeeDetail e)throws Exception
	{
		repo.add(e);
	}
	public void updateage(EmployeeDetail e)throws Exception
	{
		repo.update(e);
	}
	public String getEmp(int age)throws Exception
	{
		//System.out.println(repo.getEmployee(age));
		return repo.getEmployee(age);
	}

}
////////////////////////////////////////////////////////////////////////////
package package_test;

import static org.junit.Assert.*;

import org.junit.Test;

import package_services.EmployeeService;

public class EmpTest {

	EmployeeService es=new EmployeeService();
	
	
	
	@Test
	public void validAgeTest() throws Exception {
		assertEquals(es.getEmp(4),"Incorrect age");
	}
	
	@Test
	public void negativeAgeTest() throws Exception {
		assertEquals(es.getEmp(-5),"Incorrect age");
	}
	
	@Test
	public void correctAgeTestKishore() throws Exception {

		assertEquals(es.getEmp(23),"vraja kishore");
	}
	
	@Test
	public void correctAgeTestSundar() throws Exception {

		assertEquals(es.getEmp(43),"sundar");
	}
	
	@Test
	public void correctAgeTestSatya() throws Exception {

		assertEquals(es.getEmp(48),"satya");
	}


}

