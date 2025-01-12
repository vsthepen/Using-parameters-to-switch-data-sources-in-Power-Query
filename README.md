# Using Parameters to Dynamically Switch Data Sources in Power Query

In my workflow during analysis, I make it a point to include a step that allows me to dynamically switch data sources before proceeding with any transformations. This step is helpful when files are moved to different locations or when the organisation's database server changes. By setting this up, I can avoid the tedious process of manually reconfiguring each table source when any changes occur, especially when working with multiple tables.

## Steps to Implement Dynamic Data Source Switching

#### 1.	Create a Parameter for Data Source:
   
First, I create a new parameter called *DataSource*, which stores the folder path in the “Current Value” field. This serves as a reference for all my data sources.

![image](https://github.com/user-attachments/assets/fc3e5429-62a8-405b-a9b1-b82aa2588c82)

#### 2.	Create a Power Query Function to Filter Folder Files Dynamically:

I create a Power Query function that can filter files dynamically.

On the Home tab, I click *New Source > Blank Query*.

I then write the following M code to define the function:

![image](https://github.com/user-attachments/assets/d1311004-c4a1-4b2f-a821-07d28c578223)

Explanation of the M code:

- **(FileName as text):** Defines an input parameter for the function, expecting a *FileName*.

- **Folder.Files(DataSource):** Accesses the folder based on the folder path stored in the *DataSource* parameter.

- **each [Name] = FileName:** Filters the folder’s contents by comparing the file name with the provided *FileName*.

- **FileContent:** Returns the content of the filtered file as binary, which represents the raw CSV content before it is parsed.

I then rename the function (e.g., **GetFile**).


![image](https://github.com/user-attachments/assets/0db5ad8b-90d0-4d90-a3b2-d19817dfde75)

#### 3.	Pass the file names through the function:

I pass the filename (e.g., "sales.csv") to the FileSelection function:

![image](https://github.com/user-attachments/assets/8659e06d-bcfb-40e8-bad3-cc6afe62152e)

This step dynamically retrieves the content of the sales file based on the *DataSource* path.

#### 4.	Repeat the Process for Other Tables:

To extract additional tables, such as the "products.csv" file, I use the same function by passing the filename to it:

![image](https://github.com/user-attachments/assets/2eea236a-4a9c-415f-9d4d-8d73f2d6e5fa)

After invoking the function, I rename the queries from "Invoked Function" to something meaningful (e.g., Sales, Products).


![image](https://github.com/user-attachments/assets/659c793b-6c75-43af-bd61-295cd4deab80)

Extracting the tables by passing the *FileName* through the function ensures that both the Sales and Products tables are updated automatically whenever the *DataSource* value changes, without breaking.

With this setup, if the *DataSource* path is updated, all relevant queries (e.g., Sales and Products) will be automatically updated without the need for manual adjustments. This approach saves time and ensures consistency across multiple data sources.

#### Best Practices

- If files are moved or renamed, there’s a possibility of encountering errors if the column names change. Therefore, it’s essential to ensure column names remain consistent across data sources. To handle potential changes in column names, I typically use the *Table.RenameColumns* function to rename columns as needed or setting *MissingField.Ignore* in the arguments for handling missing fields.

- When it comes to configuring dynamic SQL queries in tools like Power Query or when creating SQL queries based on user input (such as table names, column names, or other parameters), there is a risk of **SQL injection** if the inputs are not validated. To mitigate this risk in Power Query or any other tool,I **ALWAYS** validate inputs, and ensure that only trusted parameters are used in constructing queries.

a) For example, if I have to pass a table name through a function,I restrict the input to a pre-defined list of valid table names.

b) When configuring query for the function using M code, I tend to use a stored procedure that accepts parameters, and then call it with Power Query without injecting raw SQL query into the code.

#### Please feel free to watch video below for a walk-through:

https://github.com/user-attachments/assets/939227e9-202a-4dea-8840-050349a50f3d

