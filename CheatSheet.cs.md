
Set-ExecutionPolicy unrestricted

cd C:\temp\Easy-GPU-PV

.\CopyFilesToVM.cs.ps1

.\Update-VMGpuPartitionDriver.ps1 -VMName "VMJirkrka" -GPUName "NVIDIA GeForce RTX 3060"