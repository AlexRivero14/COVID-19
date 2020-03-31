

# Resolver la siguiente situación - COVID-19

## Hoy dia 31/03/2020 he empezado a trabajar como becario de sistemas en el departamento de emergencias del Hospital de Alcorcon, Lo primero que he visto al llegar, es que tanto los medicos y las recepcionistas tienen un lio a la hora de ver las carpetas, ficheros... Tambien tienen problemas para ver la informacion de los pacientes contagiados del COVID-19.


   
 ### 1. Crear una carpeta compartida llamada EMERGENCIAS
  
```PowerShell 	
New-SmbShare -Name fso -Path E:\EMERGENCIAS –FullAccess Administrador -ReadAccess Everyone
```

 ### 2. Crearemos un acceso directo el cual tengan permisos todos los trabajadores que formen parte de EMERGENCIAS
 
```PowerShell 
 New-PSDrive -Name "E" -PSProvider Filesystem -Root \\Laptop-s5ls1du9\EMERGENCIAS -Persist
```

 ### 3. Dentro de esa carpeta crearemos a todos los usuarios que entren por emergencias y los meteremos en grupos
 
```PowerShell 
 gc E:\EMERGENCIAS.txt | %{
    new-aduser -name (($_).split(",")[0]) -sam (($_).split(",")[0]) -accountpassword (convertto-securestring "COVID19" -asplaintext -force) -enable $true
    Add-ADGroupMember (($_).split(",")[1]) -Members (($_).split(",")[0])
    }
Start-Sleep -Seconds 1
```

 ### 4. Una vez realizado todo esto crearemos un formulario para que los medicos cuando realizen las pruebas confirem si el paciente tienen el COVID o no
 
```PowerShell 
$buttons_functionslist=@(
	"SI"
	"NO"
)

$buttons_functionbuttoncount = $buttons_functionslist.count

$loop = 0

#Formulario
$Form = New-Object System.Windows.Forms.Form
$Form.Text="TIENE COVID-19"
$Form.Size=New-Object System.Drawing.Size(500,300)
$Form.StartPosition="CenterScreen"

while($loop -lt $buttons_functionbuttoncount)
	{
	$thisbutton = New-Object System.Windows.Forms.Button
	[string]$thisbuttonname = $buttons_functionslist[$loop]
	$thisbutton.Text = $thisbuttonname
	$thisbutton.size = New-Object System.Drawing.Size(100,20)
	$thisbutton.Location = New-Object System.Drawing.Size(200,(20*$loop+1))
	$thisbutton.Add_Click({
	        Write-Host $this.Text
		})
	$Form.Controls.Add($thisbutton)
	$loop += 1
	}

$Form.ShowDialog()
```
