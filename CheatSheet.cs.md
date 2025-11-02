
.\CopyFilesToVM.ps1 -VMName "VMJirkrka" `
 -VHDPath "V:\HYPER-V\VMJirkrka\" `
 -UnattendPath "$PSScriptRoot\autounattend.cs.xml" `
 -SourcePath "C:\temp\cs-cz_windows_11_business_editions_version_25h2_x64_dvd_544cec92.iso" `
 -SizeBytes 440gb `
 -MemoryAmount 8GB `
 -CPUCores 6 `
 -GPUName "NVIDIA GeForce RTX 3060" `
 -Username "Doma" `
 -Password "doma" `
 -Autologon "true" `
 -GPUResourceAllocationPercentage 40 `
 -NetworkSwitch "Externi" `
 -Edition 6 `
 -VhdFormat "VHDX" `
 -DiskLayout "UEFI"


.\Update-VMGpuPartitionDriver.ps1 -VMName "VMJirkrka" -GPUName "NVIDIA GeForce RTX 3060"