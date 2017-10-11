
<a name="cs_0_csharpprogramexample_h2"/>

## <a name="c-program-example"></a>C# 程式範例

本文的下一節會呈現使用 ADO.NET 將 TRANSACT-SQL 陳述式傳送到 SQL 資料庫的 C# 程式。 C# 程式會執行下列動作：

1. [使用 ADO.NET 連線至 SQL 資料庫](#cs_1_connect)。
2. [建立資料表](#cs_2_createtables)。
3. [在資料表中填入資料，藉由發出 T-SQL INSERT 陳述式](#cs_3_insert)。
4. [使用聯結更新資料](#cs_4_updatejoin)。
5. [使用聯結刪除資料](#cs_5_deletejoin)。
6. [使用聯結選取資料列](#cs_6_selectrows)。
7. 關閉連線 (這會從 tempdb 置放任何暫存資料表)。

C# 程式包含：

- C# 程式碼，用來連線至資料庫。
- 可傳回 T-SQL 原始程式碼的方法。
- 將 T-SQL 提交至資料庫的兩種方法。

#### <a name="to-compile-and-run"></a>編譯和執行

這個 C# 程式邏輯上是一個 .cs 檔案。 但是，程式在此會實際分成數個程式碼區塊，讓您更容易查看和了解每個區塊。 若要編譯及執行此程式，請執行下列作業：

1. 在 Visual Studio 中建立 C# 專案。
    - 專案類型應該是*主控台*應用程式，從結果類似下列階層：**範本** > **Visual C#** > **傳統的 Windows 桌面** > **主控台應用程式 (.NET Framework)**。
3. 在 **Program.cs** 檔案中，清除程式碼的小型起始行。
3. 在 Program.cs 中，複製並貼上以下每個區塊 (以其在此顯示的相同順序)。
4. 在 Program.cs 中，編輯 **Main** 方法中的下列值：

   - **cb.DataSource**
   - **cd.UserID**
   - **cb.Password**
   - **InitialCatalog**

5. 確認已參照 **System.Data.dll** 組件。 若要確認，請展開 [方案總管] 窗格中的 [參考] 節點。
6. 若要在 Visual Studio 中建置程式，請按一下 [建置] 功能表。
7. 若要從 Visual Studio 執行程式，請按一下 [啟動] 按鈕。 報告輸出會顯示在 cmd.exe 視窗中。

> [!NOTE]
> 您可以選擇編輯 T-SQL，將前置  **#**  新增至資料表名稱，以將其建立為 **tempdb** 中的暫存資料表。 沒有測試資料庫可供使用時，這很適合用於示範。 暫存資料表會在連線關閉時自動刪除。 不會對暫存資料表強制執行外部索引鍵的任何 REFERENCES。
>

<a name="cs_1_connect"/>
### <a name="c-block-1-connect-by-using-adonet"></a>C# 區塊 1：使用 ADO.NET 連線

- [下一頁](#cs_2_createtables)


```csharp
using System;
using System.Data.SqlClient;   // System.Data.dll 
//using System.Data;           // For:  SqlDbType , ParameterDirection

namespace csharp_db_test
{
   class Program
   {
      static void Main(string[] args)
      {
         try
         {
            var cb = new SqlConnectionStringBuilder();
            cb.DataSource = "your_server.database.windows.net";
            cb.UserID = "your_user";
            cb.Password = "your_password";
            cb.InitialCatalog = "your_database";

            using (var connection = new SqlConnection(cb.ConnectionString))
            {
               connection.Open();

               Submit_Tsql_NonQuery(connection, "2 - Create-Tables",
                  Build_2_Tsql_CreateTables());

               Submit_Tsql_NonQuery(connection, "3 - Inserts",
                  Build_3_Tsql_Inserts());

               Submit_Tsql_NonQuery(connection, "4 - Update-Join",
                  Build_4_Tsql_UpdateJoin(),
                  "@csharpParmDepartmentName", "Accounting");

               Submit_Tsql_NonQuery(connection, "5 - Delete-Join",
                  Build_5_Tsql_DeleteJoin(),
                  "@csharpParmDepartmentName", "Legal");

               Submit_6_Tsql_SelectEmployees(connection);
            }
         }
         catch (SqlException e)
         {
            Console.WriteLine(e.ToString());
         }
         Console.WriteLine("View the report output here, then press any key to end the program...");
         Console.ReadKey();
      }
```


<a name="cs_2_createtables"/>
### <a name="c-block-2-t-sql-to-create-tables"></a>C# 區塊 2：T-SQL 用以建立資料表

- [上一個](#cs_1_connect) &nbsp; / &nbsp; [下一個](#cs_3_insert)

```csharp
      static string Build_2_Tsql_CreateTables()
      {
         return @"
DROP TABLE IF EXISTS tabEmployee;
DROP TABLE IF EXISTS tabDepartment;  -- Drop parent table last.


CREATE TABLE tabDepartment
(
   DepartmentCode  nchar(4)          not null
      PRIMARY KEY,
   DepartmentName  nvarchar(128)     not null
);

CREATE TABLE tabEmployee
(
   EmployeeGuid    uniqueIdentifier  not null  default NewId()
      PRIMARY KEY,
   EmployeeName    nvarchar(128)     not null,
   EmployeeLevel   int               not null,
   DepartmentCode  nchar(4)              null
      REFERENCES tabDepartment (DepartmentCode)  -- (REFERENCES would be disallowed on temporary tables.)
);
";
      }
```

#### <a name="entity-relationship-diagram-erd"></a>實體關聯圖 (ERD)

先前的 CREATE TABLE 陳述式包含 **REFERENCES** 關鍵字，可建立兩個資料表之間的「外部索引鍵」(FK) 關聯性。  如果您使用 tempdb，請使用一對前置破折號將 `--REFERENCES` 關鍵字註解化。

接著是顯示兩個資料表之間關聯性的 ERD。 #tabEmployee.DepartmentCode「子」資料行中的值受限於 #tabDepartment.Department「父」資料行中顯示的值。

![顯示外部索引鍵的 ERD](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)


<a name="cs_3_insert"/>
### <a name="c-block-3-t-sql-to-insert-data"></a>C# 區塊 3：T-SQL 用以插入資料

- [上一個](#cs_2_createtables) &nbsp; / &nbsp; [下一個](#cs_4_updatejoin)


```csharp
      static string Build_3_Tsql_Inserts()
      {
         return @"
-- The company has these departments.
INSERT INTO tabDepartment
   (DepartmentCode, DepartmentName)
      VALUES
   ('acct', 'Accounting'),
   ('hres', 'Human Resources'),
   ('legl', 'Legal');

-- The company has these employees, each in one department.
INSERT INTO tabEmployee
   (EmployeeName, EmployeeLevel, DepartmentCode)
      VALUES
   ('Alison'  , 19, 'acct'),
   ('Barbara' , 17, 'hres'),
   ('Carol'   , 21, 'acct'),
   ('Deborah' , 24, 'legl'),
   ('Elle'    , 15, null);
";
      }
```


<a name="cs_4_updatejoin"/>
### <a name="c-block-4-t-sql-to-update-join"></a>C# 區塊 4：T-SQL 用以更新聯結

- [上一個](#cs_3_insert) &nbsp; / &nbsp; [下一個](#cs_5_deletejoin)


```csharp
      static string Build_4_Tsql_UpdateJoin()
      {
         return @"
DECLARE @DName1  nvarchar(128) = @csharpParmDepartmentName;  --'Accounting';


-- Promote everyone in one department (see @parm...).
UPDATE empl
   SET
      empl.EmployeeLevel += 1
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName1;
";
      }
```


<a name="cs_5_deletejoin"/>
### <a name="c-block-5-t-sql-to-delete-join"></a>C# 區塊 5：T-SQL 用以刪除聯結

- [上一個](#cs_4_updatejoin) &nbsp; / &nbsp; [下一個](#cs_6_selectrows)


```csharp
      static string Build_5_Tsql_DeleteJoin()
      {
         return @"
DECLARE @DName2  nvarchar(128);
SET @DName2 = @csharpParmDepartmentName;  --'Legal';


-- Right size the Legal department.
DELETE empl
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName2

-- Disband the Legal department.
DELETE tabDepartment
   WHERE DepartmentName = @DName2;
";
      }
```



<a name="cs_6_selectrows"/>
### <a name="c-block-6-t-sql-to-select-rows"></a>C# 區塊 6：T-SQL 用以選取資料列

- [上一個](#cs_5_deletejoin) &nbsp; / &nbsp; [下一個](#cs_6b_datareader)


```csharp
      static string Build_6_Tsql_SelectEmployees()
      {
         return @"
-- Look at all the final Employees.
SELECT
      empl.EmployeeGuid,
      empl.EmployeeName,
      empl.EmployeeLevel,
      empl.DepartmentCode,
      dept.DepartmentName
   FROM
      tabEmployee   as empl
      LEFT OUTER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   ORDER BY
      EmployeeName;
";
      }
```


<a name="cs_6b_datareader"/>
### <a name="c-block-6b-executereader"></a>C# 區塊 6b：ExecuteReader

- [上一個](#cs_6_selectrows) &nbsp; / &nbsp; [下一個](#cs_7_executenonquery)

這個方法設計用來執行 **Build_6_Tsql_SelectEmployees** 方法所建置的 T-SQL SELECT 陳述式。


```csharp
      static void Submit_6_Tsql_SelectEmployees(SqlConnection connection)
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("Now, SelectEmployees (6)...");

         string tsql = Build_6_Tsql_SelectEmployees();

         using (var command = new SqlCommand(tsql, connection))
         {
            using (SqlDataReader reader = command.ExecuteReader())
            {
               while (reader.Read())
               {
                  Console.WriteLine("{0} , {1} , {2} , {3} , {4}",
                     reader.GetGuid(0),
                     reader.GetString(1),
                     reader.GetInt32(2),
                     (reader.IsDBNull(3)) ? "NULL" : reader.GetString(3),
                     (reader.IsDBNull(4)) ? "NULL" : reader.GetString(4));
               }
            }
         }
      }
```


<a name="cs_7_executenonquery"/>
### <a name="c-block-7-executenonquery"></a>C# 區塊 7：ExecuteNonQuery

- [上一個](#cs_6b_datareader) &nbsp; / &nbsp; [下一個](#cs_8_output)

系統會對修改資料表之資料內容的作業呼叫這個方法，而不需傳回任何資料列。


```csharp
      static void Submit_Tsql_NonQuery(
         SqlConnection connection,
         string tsqlPurpose,
         string tsqlSourceCode,
         string parameterName = null,
         string parameterValue = null
         )
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("T-SQL to {0}...", tsqlPurpose);

         using (var command = new SqlCommand(tsqlSourceCode, connection))
         {
            if (parameterName != null)
            {
               command.Parameters.AddWithValue(  // Or, use SqlParameter class.
                  parameterName,
                  parameterValue);
            }
            int rowsAffected = command.ExecuteNonQuery();
            Console.WriteLine(rowsAffected + " = rows affected.");
         }
      }
   } // EndOfClass
}
```


<a name="cs_8_output"/>
### <a name="c-block-8-actual-test-output-to-the-console"></a>C# 區塊 8：實際測試輸出到主控台

- [上一個](#cs_7_executenonquery)

本節會擷取程式傳送至主控台的輸出。 只有 guid 值會在測試回合之間改變。


```text
[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>> csharp_db_test.exe

=================================
Now, CreateTables (10)...

=================================
Now, Inserts (20)...

=================================
Now, UpdateJoin (30)...
2 rows affected, by UpdateJoin.

=================================
Now, DeleteJoin (40)...

=================================
Now, SelectEmployees (50)...
0199be49-a2ed-4e35-94b7-e936acf1cd75 , Alison , 20 , acct , Accounting
f0d3d147-64cf-4420-b9f9-76e6e0a32567 , Barbara , 17 , hres , Human Resources
cf4caede-e237-42d2-b61d-72114c7e3afa , Carol , 22 , acct , Accounting
cdde7727-bcfd-4f72-a665-87199c415f8b , Elle , 15 , NULL , NULL

[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>>
```
