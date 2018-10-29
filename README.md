# hello-world
learning github repo
just going through the tutorial to learn how to use github.
adding this for no real reason.
this also
#ERASE ALL THIS AND PUT XAML BELOW between the @" "@
$inputXML = @"
<Window x:Class="powerShell_test.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:powerShell_test"
        mc:Ignorable="d"
        Title="Powershell Test" Height="350" Width="525">
    <Grid>
        <Image x:Name="image" HorizontalAlignment="Left" Height="126" Margin="10,10,0,0" VerticalAlignment="Top" Width="497" Source="pressBuddy2.png"/>
        <TextBlock x:Name="textBlock" HorizontalAlignment="Left" Height="84" Margin="49,150,0,0" TextWrapping="Wrap" Text="Use this tool to find out all sorts of useful disk information, and also to get rich input from your scripts and tools" VerticalAlignment="Top" Width="418" IsEnabled="False" FontFamily="Palatino Linotype" FontSize="18"/>
        <Button x:Name="button" Content="OK" HorizontalAlignment="Left" Margin="390,276,0,0" VerticalAlignment="Top" Width="75" FontFamily="Palatino Linotype" FontSize="14"/>
        <Label x:Name="label" Content="User Name:" HorizontalAlignment="Left" Margin="49,239,0,0" VerticalAlignment="Top" FontFamily="Palatino Linotype" FontSize="14"/>
        <TextBox x:Name="textBox" HorizontalAlignment="Left" Height="23" Margin="49,276,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="197"/>

    </Grid>
</Window>
"@       
 
$inputXML = $inputXML -replace 'mc:Ignorable="d"','' -replace "x:N",'N'  -replace '^<Win.*', '<Window'
 
[void][System.Reflection.Assembly]::LoadWithPartialName('presentationframework')
[xml]$XAML = $inputXML
#Read XAML
 
    $reader=(New-Object System.Xml.XmlNodeReader $xaml)
try{$Form=[Windows.Markup.XamlReader]::Load( $reader )}
catch{Write-Warning "Unable to parse XML, with error: $($Error[0])`n Ensure that there are NO SelectionChanged properties (PowerShell cannot process them)"
    throw}
 
#===========================================================================
# Load XAML Objects In PowerShell
#===========================================================================
  
$xaml.SelectNodes("//*[@Name]") | %{"trying item $($_.Name)";
    try {Set-Variable -Name "WPF$($_.Name)" -Value $Form.FindName($_.Name) -ErrorAction Stop}
    catch{throw}
    }
 
Function Get-FormVariables{
if ($global:ReadmeDisplay -ne $true){Write-host "If you need to reference this display again, run Get-FormVariables" -ForegroundColor Yellow;$global:ReadmeDisplay=$true}
write-host "Found the following interactable elements from our form" -ForegroundColor Cyan
get-variable WPF*
}
 
Get-FormVariables
 
#===========================================================================
# Actually make the objects work
#===========================================================================
 
#Sample entry of how to add data to a field
 
#$vmpicklistView.items.Add([pscustomobject]@{'VMName'=($_).Name;Status=$_.Status;Other="Yes"})
 
#===========================================================================
# Shows the form
#===========================================================================
write-host "To show the form, run the following" -ForegroundColor Cyan
'$Form.ShowDialog() | out-null'
