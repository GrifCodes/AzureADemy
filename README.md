![AzureADemy banner](https://github.com/GrifCodes/AzureADemy/assets/150773923/1ba3c8d9-4778-48f7-a0de-3977a85ed87f)

# AzureADemy: Showcasing My Azure Journey

## Phase 1: Setup and Connectivity

![phase1withscreens](https://github.com/GrifCodes/AzureADemy/assets/150773923/d6e4eb69-fa9f-433e-a501-bf0524ffb074)


In this phase, I lay the groundwork in Azure:
- I set up a Domain Controller VM (Windows Server 2022) named “DC-1”, carefully selecting the right specs for efficiency.
- I make a note of the Resource Group and Virtual Network (Vnet) and ensure the Domain Controller’s NIC Private IP address is static.
- The Client VM (Windows 10), named “Client-1”, joins in, using the same Resource Group and Vnet – crucial for seamless network communication.

Connectivity is key:
- I initiate a connection to Client-1 with Remote Desktop and test the network with `ping -t <DC-1's private IP address>`.
- Over on DC-1, I enable ICMPv4 in the local Windows Firewall.
- After making these adjustments, I confirm the ping success from Client-1, ensuring solid communication between the VMs.
----------------
## Phase 2: Active Directory Setup and Domain Joining

![Phase2withscreens](https://github.com/GrifCodes/AzureADemy/assets/150773923/58d5cd5e-1309-466a-aecd-fef2d8c912bf)


Active Directory is at the heart of this phase:
- Logging into DC-1, I install and configure Active Directory Domain Services, creating a new forest named `ad.sandbox`.
- This process is akin to laying the digital foundation for a company's IT infrastructure.

Client-1 joins the domain:
- I update Client-1’s DNS settings in Azure Portal to align with DC’s Private IP address.
- After a restart, I join Client-1 to the `ad.sandbox` domain.
- I log into the Domain Controller to confirm Client-1's registration in ADUC under the “Computers” container.
- Additionally, I create a new OU named “_CLIENTS” and organize Client-1 into it, enhancing the domain's structure.
---------------
## Phase 3: User Management and Remote Desktop Setup

![phase3withscreens](https://github.com/GrifCodes/AzureADemy/assets/150773923/5812de2c-d0a4-4a47-b494-3b35a346cd77)


This phase is all about user management:
- I create Organizational Units (OUs) named “_EMPLOYEES” and “_ADMINS”.
- A new user, “Jane Doe” with username “jane_admin”, is created and added to the “Domain Admins” group.
- After logging out, I log back in as “mydomain.com\jane_admin” to fully transition to the admin role.

Remote Desktop setup for all:
- Accessing Client-1 as `ad.sandbox\jane_admin`, I enable Remote Desktop for “domain users”.
- This step is critical for facilitating remote access and flexible working environments.
- I also consider implementing this via Group Policy in the future for broader application.

PowerShell in Action:
- This script automates the creation of user accounts in Active Directory. It generates 10,000 users with random names, using a mix of consonants and vowels. Each user is assigned a default password ("Password1") and is placed in the "_EMPLOYEES" organizational unit. The script efficiently handles account creation, displaying the progress in the console.

```powershell
# PowerShell script to generate user accounts
 # ----- Edit these Variables for your own Use Case ----- #
$PASSWORD_FOR_USERS   = "Password1"
$NUMBER_OF_ACCOUNTS_TO_CREATE = 10000
# ------------------------------------------------------ #

Function generate-random-name() {
    $consonants = @('b','c','d','f','g','h','j','k','l','m','n','p','q','r','s','t','v','w','x','z')
    $vowels = @('a','e','i','o','u','y')
    $nameLength = Get-Random -Minimum 3 -Maximum 7
    $count = 0
    $name = ""

    while ($count -lt $nameLength) {
        if ($($count % 2) -eq 0) {
            $name += $consonants[$(Get-Random -Minimum 0 -Maximum $($consonants.Count - 1))]
        }
        else {
            $name += $vowels[$(Get-Random -Minimum 0 -Maximum $($vowels.Count - 1))]
        }
        $count++
    }

    return $name

}

$count = 1
while ($count -lt $NUMBER_OF_ACCOUNTS_TO_CREATE) {
    $fisrtName = generate-random-name
    $lastName = generate-random-name
    $username = $fisrtName + '.' + $lastName
    $password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force

    Write-Host "Creating user: $($username)" -BackgroundColor Black -ForegroundColor Cyan
    
    New-AdUser -AccountPassword $password `
               -GivenName $firstName `
               -Surname $lastName `
               -DisplayName $username `
               -Name $username `
               -EmployeeID $username `
               -PasswordNeverExpires $true `
               -Path "ou=_EMPLOYEES,$(([ADSI]`"").distinguishedName)" `
               -Enabled $true
    $count++
}

```
---------------------
## Conclusion and Next Steps

That wraps up the AzureADemy journey! Through these phases, I've demonstrated key Azure administration skills—setting up and managing virtual machines, configuring Active Directory, and automating user account creation with PowerShell.

Moving forward, I aim to expand on these foundations by exploring advanced features like Azure Automation, implementing Group Policies, and delving into Azure Active Directory. The journey of learning and improvement never ends.

I'm open to suggestions and collaborations, so if you have ideas or see opportunities for enhancement, feel free to reach out or contribute to this project.

Thank you for following along!

