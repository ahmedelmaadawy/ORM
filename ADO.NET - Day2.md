# Disconnected mode

## 1- getting Data
1. Install `Microsoft.data.sqlclient`
2. Write the Select Command 
3. Container :`sqlDataAdaptor => adaptor`
4. Attach select command with adaptor 
5. `adaptor.fill(dataTable)` this will open connection then execute select command and will put data in `datatable` then it will close the connection

```c#
class Form1:Form{
	DataTable dataTable;
	SqlDataAdapter adapter ;
	SqlConnection connection;
	
	//constructor
	public Form1(){
	//1- define connection string
	string ConnectionString =
		'server=.;database=ITI;trust server certificat= true;';
		
	//2-define sql connection
	connection = new SqlConnection(ConnectionString);
	//define adapter 
	adapter  = new SqlDataAdapter();
	}
	private void Form1_Load(object sender ,EventArgs e){
		GettinStudentData();
		nextId = (int) dataTable.rows[dataTable.rows -1]["Id"]+1;
		GetDepartments();
	}
	void GettingStudentData(){
	//3- define sql command
	SqlCommand cmd = new SqlCommand('select * from student',Connection);
	//4- attach command to adapter
	adapter.SelectCommand = cmd ;
	//5- get data
	dataTable = new DataTable();
	adapter.Fill(dataTable);
	//6- present data
	dgv_student = dataTable;
	}
}
```

For Departments 
```c#
private void GetDepartment(){
	SqlCommand cmd = new sqlCommand('select * from departments',Connection);
	adapter.SelectCommand = cmd ;
	DataTable depts = new DataTable();
	adapter.Fill(depts);
	cb_depts.DataSource = depts;
	cb_depts.DisplayMember = "Name";
	cb_depts.ValueMember = "Id";
}
```
`Binding between dataTable and data grid view => is Bi Direction`
___
## 2- During Using app
**insert - update - delete in `InMemory`**
`dataTable` has property called `row status` for every row to track changes in rows
- `unchanged`
- `modified`
- `deleted`
- `added`

Add Button 
```c#
private void btn_add_click(object sender , EventArgs e){

	DataRow row = datatable.NewRow();
	row["name"] = txt_name.text ;
	row["age"] = nud_age.Value ;
	row["address"] = txt_address.Text; 
	row["Dept-id"] = cb_depts.SelectedValue;
	row["Id"] = nextId ;
}

```

```C#

public void btn_save_btn(){
	foreach(DataRow row in dataTable){
		Debug.WriteLine(row.RowState);
	}
	SqlCommand insertCommand =
	 new SqlCommand("insert into student Values(@name,@age,@address,@dept)",connection);
		insertCommand.Parameters.Add("name",sqlDbpropType.NVarChar,50,"Name");
	insertCommand.Parameters.Add("age",sqlDbType.Int,4,"Age");
	insertCommand.Parameters.Add("address",sqlDbType.NVarchar,50,
	"Address");
	insertCommand.Parameters.Add("dept",sqlDbType.Int,4,"DeptId");
	SqlCommand updateCommand = new SqlCommand("update student set ",connection);
	SqlCommand deleteCommand = new SqlCommand("delete from student where",connection);

	//attach commands to adapter
	adapter.InsertCommand = insertCommand;
	adapter.UpdateCommand = UpdateCommand;
	adapter.DeleteCommand = DeleteCommand ;

	//Synchronization
	adapter.Update(dataTable);

	GetStudentData();
}
```
## 3- Synchronization data with DB
1. we will synchronize every period of time. 
2. During synchronization you will need to define three commands for `insert` and `update` and `delete` commands.
3. Attach commands to`Adaptor` 
4. `adaptor.insertcommand = "insert command"`
5. `adaptor.updatecommand = "update command"`
6. `adaptor.deletecommand = "delete command"`
7. Update Database `adaptor.Update(dataTable)` 
	1. This will open connection 
	2. Check with every row 
	3. Reflect changes in database
	4. Close Connection
8. After synchronizing data we need to get data again to restart status of rows in `datatable`










