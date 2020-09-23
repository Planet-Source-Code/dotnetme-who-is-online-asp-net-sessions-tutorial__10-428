<div align="center">

## Who is online ASP\.NET \(Sessions tutorial\)


</div>

### Description

This tutorial shows how you can list the users authenticated in your site using session object.
 
### More Info
 


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[dotnetme](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/dotnetme.md)
**Level**          |Intermediate
**User Rating**    |4.2 (25 globes from 6 users)
**Compatibility**  |VB\.NET, ASP\.NET
**Category**       |[Internet/ Browsers/ HTML](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/internet-browsers-html__10-9.md)
**World**          |[\.Net \(C\#, VB\.net\)](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/net-c-vb-net.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/dotnetme-who-is-online-asp-net-sessions-tutorial__10-428/archive/master.zip)





### Source Code

<P>
Ok, here is a code snipet to determine who is online on your web site.
Essentially there are 2 tables Users and OnlineUsers. I did it this way to not attach the
sessionID field to each user (text about 20 chars long) and waste DB space.
<P>
the UserID can be autonumeric (PK) .
<P>
First of all, when a user log in, we insert a record on the OnlineUsers table. The values
are the UsserID that loged in (duh) and the sessionID property of the Session Object.
<P>
Here is a code snippet: (for the login form, assuming you are using forms authentication)
<P>
Private Sub LoginButton_Click(ByVal sender As System.Object, ByVal e As System.EventArgs)
Handles LoginButton.Click <br>
Response.cookies(“UserID”)=txtUsername.Text <br>
Dim cnn as new oledb.oledbconnection(ConfigurationSettings.Appsettings(“cnnString”)<br>
Dim strInsert as string = “Insert into OnlineUsers (UserID,SessionID) Values(‘ “ &
txtUserName.Text & “’,’” & Session.SessionID & “’)” <br>
Dim oldbInsert as new oledbCommand(strInsert,cnn)<br>
Cnn.open()<br>
OldbInsert.ExecuteNonQuery()<br>
Dim strUpdate as string = “Update Users set Online = True where UserID =’“ &
txtUserName.Text & “’” <br>
Dim oldbUpdate as new oledbCommand(strUpdate,cnn)<br>
OldbUpdate.ExecuteNonQuery()<br>
<br>
End Sub<br>
<br>
Now, when the users click logout somewhere in your page:<br>
<br>
Private Sub LogoutButton_Click(ByVal sender As System.Object, ByVal e As System.EventArgs)
Handles LogoutButton.Click<br>
<br>
Dim cnn as new oledb.oledbconnection(ConfigurationSettings.Appsettings(“cnnString”)<br>
Dim strDelete as string = “Delete from OnlineUsers where SessionID =’” & Session.SessionID &
“’” <br>
<br>
Dim oldbDelete as new oledbCommand(strdelete,cnn)<br>
Cnn.open()<br>
OldbDelete.ExecuteNonQuery()<br>
<br>
<br>
Dim strUpdate as string = “Update Users set Online = false where UserID =’“ &
Response.cookies(“UserID”).value & “’” <br>
Dim oldbUpdate as new oledbCommand(strUpdate,cnn)<br>
OldbUpdate.ExecuteNonQuery()<br>
<br>
End Sub
<br>
<br>
Now, what for am I using the session ID?<br>
<br>
What happens if a user doesn’t press the logout button? It will be online until it hits the
logout button.<br>
<br>
So in your Session_End event. In your Global.asx.vb you can put this code so when the
session expires, the app automatically deletes the record from the users table and put the
online status = False.<br>
<br>
Sub Session_End(ByVal sender As Object, ByVal e As EventArgs) <br>
<br>
 Dim cnn As New<br>
OleDb.OleDbConnection(ConfigurationSettings.AppSettings("ConnectionString") &
ConfigurationSettings.AppSettings("DBPath")) <br>
 Dim strUserID As String = "" <br>
 Dim rdr As OleDb.OleDbDataReader <br>
 Dim com As New OleDb.OleDbCommand("Select UserID from OnlineUsers where SessionID
='" & Session.SessionID & "'", cnn) <br>
<br>
 cnn.Open() <br>
 rdr = com.ExecuteReader <br>
 While rdr.Read() <br>
  strUserID = CStr(rdr.GetValue(0)) <br>
 End While <br>
 rdr.Close() <br>
 If strUserID <> "" Then <br>
  com.CommandText = "Delete from OnlineUsers where UserID =" & strUserID <br>
  com.ExecuteNonQuery() <br>
  com.CommandText = "Update Users Set Online = False where UserID =" & strUserID
<br>
  com.ExecuteNonQuery() <br>
 End If <br>
<br>
 cnn.Close() <br>
 End Sub<br>
<br>
Now you can present the users with the Online=true on a datagrid or other bound control =)
<br>
Note:<br>
You cannot call subs or functions from the session_end events, or it will not execute.<br>
<br>
Also When an error occurs you don’t get notified, even in debug, because this is executed in
a background process.<br>
<br>
So I hope you learn a little from this tut, I passed long hours to get to this =)<br>
<br>
And a final note: sorry about my English, I’m from Mexico (heheh)<br>
<br>
Vote!

