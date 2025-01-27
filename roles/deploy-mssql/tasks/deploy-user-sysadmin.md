# Create Azure DevOps Server user in MSSQL Database

This SQL Query was used to create a user with sysadmin privileges. I don't think this is needed anymore going forward. But saving it just in case I might need it.

```
    # Add user to sysadmin role if not already there
    $Query = @"
    DECLARE @Message NVARCHAR(255);
    IF NOT EXISTS (
        SELECT 1 
        FROM sys.server_role_members rm
        JOIN sys.server_principals sp_role ON rm.role_principal_id = sp_role.principal_id
        JOIN sys.server_principals sp_member ON rm.member_principal_id = sp_member.principal_id
        WHERE sp_role.name = 'sysadmin' AND sp_member.name = '$SQLUser'
    )
    BEGIN
        IF NOT EXISTS (SELECT 1 FROM sys.server_principals WHERE name = '$SQLUser')
        BEGIN
            CREATE LOGIN [$SQLUser] FROM WINDOWS;
        END
        EXEC sp_addsrvrolemember @loginame = N'$SQLUser', @rolename = N'sysadmin';
        SET @Message = 'User added';
    END
    ELSE
    SET @Message = 'User already exists';
    SELECT @Message AS ResultMessage;
    "@
```
