windows-docker-role
=========

This role installs Docker on a Windows server using the officially supported methods provided at https://docs.microsoft.com/en-us/virtualization/windowscontainers/quick-start/set-up-environment?tabs=Windows-Server#prerequisites. **Note that Windows requires a reboot following the installation of the Containers feature and this rike reboots the server if necessary.**

Requirements
------------

- Ansible 2.10 or above. Some of the community.windows collection's modules used in this role require it.
- Tested on Windows Server 2019 Core running within Hyper-V but should run with anything above Windows Server 2016 according to the Microsoft documentation (see above)

Role Variables
--------------

- `setup_firewall_rules`: Open all Docker ports using the defined `docker.firewallprofile` profile. Defaults to false.
- `nuget_minimum_version`: The minimum desired version of NuGet to install. Defaults to 2.8.5.201 which is required for the tasks to run.
- `expected_nuget_minimum_version`: The minimum versionof NuGet required for the tasks to run. Placed here in case something is updated and this version changes. Defaults to 2.8.5.201 which is the current requirement.
- `powershell.install_reboot_timeout`: The time to wait for the server coming back online following powershell's installation if a reboot is required. Defaults to 600, which happens to be (but it not based off) the default reboot_timeout value.
- `powershellget.minimum_version`: The minimum version of PowerShellGet to install. Defaults to 2.2.5 which is the lowest supported version for some of the tasks at this time.
- `docker.install_reboot_timeout`: The time to wait for the server to come back online following Docker installation. Defaults to 1200.
- `docker.version`: The version of docker to install based on the version list provided in the documentation in the summary. Some versions are applicable to Docker EE Engine or Docker Enterprise Engine. You can put in values like 18.03. Defaults to latest.
- `docker.firewallprofile`: The profile to apply the firewall rules to. Defaults to Domain.
- `powershell.msi_address`: The address for the PowerShell MSI file to install. Defaults to https://github.com/PowerShell/PowerShell/releases/download/v7.1.3/PowerShell-7.1.3-win-x64.msi which is the current latest.
- `powershell.product_id`: The product ID to install from the archive. Defaults to '{6488FC90-3CAB-4246-A212-1AD7BDE685F7}'.

Dependencies
------------

- community.windows

Example Playbook
----------------

Install the latest version of Docker, don't open ports:

```
  - hosts: servers
    roles:
        - role: windows-docker-role
```

Install and open ports for the Domain firewall profile:
```
  - hosts: servers
    roles:
        - role: windows-docker-role
          setup_firewall_rules: true
```

Install and open ports for ALL firewall profiles:
```
  - hosts: servers
    roles:
        - role: windows-docker-role
          setup_firewall_rules: true
          #Must initialize the entire docker object https://github.com/jimbo8098/windows-docker-role/issues/2
          docker:
            install_reboot_timeout: 1200
            version: latest
            firewallprofile: domain,private,public
```

Install a specific version of Docker:
```
  - hosts: servers
    roles:
        - role: windows-docker-role
          docker:
            version: 18.03
```

License
-------

BSD

Author Information
------------------

