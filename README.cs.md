# Easy-GPU-PV

Rozpracovaný projekt (work-in-progress) věnovaný zjednodušení GPU Paravirtualizace (GPU-PV) na Windows Hyper-V!

GPU-PV vám umožňuje rozdělit dedikovanou nebo integrovanou GPU vašeho systému a přiřadit ji několika virtuálním strojům (VM) v Hyper-V. Je to stejná technologie, která se používá ve WSL2 a Windows Sandbox.

Easy-GPU-PV si klade za cíl toto usnadnit automatizací kroků potřebných pro zprovoznění GPU-PV VM.

Easy-GPU-PV dělá následující...

1.  Vytvoří VM dle vašeho výběru
2.  Automaticky nainstaluje Windows do VM
3.  Rozdělí vámi zvolenou GPU a zkopíruje požadované soubory ovladačů do VM

Poté byste měli nainstalovat aplikaci pro vzdálenou plochu / streamování her akcelerovanou přes GPU, stejně jako ovladač virtuálního displeje, abyste z VM dostali maximum. Podívejte se na záložku "Issues" (Problémy) v tomto repozitáři pro doporučení.

### Předpoklady:

* Windows 10 20H1+ Pro, Enterprise nebo Education NEBO Windows 11 Pro, Enterprise nebo Education. Windows 11 na hostiteli i VM je preferován kvůli lepší kompatibilitě.
* Shodné verze Windows mezi hostitelem a VM. Neshody mohou způsobit problémy s kompatibilitou, modré obrazovky (blue-screens) nebo jiné potíže. (Např. Win10 21H1 + Win10 21H1, nebo Win11 21H2 + Win11 21H2)
* Stolní počítač s dedikovanou GPU NVIDIA/AMD nebo integrovanou GPU Intel - Notebooky s GPU NVIDIA nejsou v tuto chvíli podporovány, ale integrované GPU Intel na noteboocích fungují. GPU musí podporovat hardwarové kódování videa (NVIDIA NVENC, Intel Quicksync nebo AMD AMF).
* Nejnovější ovladač GPU z Intel.com nebo NVIDIA.com, nespoléhejte se na Správce zařízení nebo Windows Update.
* Nejnovější ISO Windows 10 [stažené odtud](https://www.microsoft.com/en-gb/software-download/windows10ISO) / ISO Windows 11 [stažené odtud.](https://www.microsoft.com/en-us/software-download/windows11) - Nepoužívejte Media Creation Tool, pokud není k dispozici přímý odkaz na ISO, postupujte podle [tohoto průvodce.](https://www.nextofwindows.com/downloading-windows-10-iso-images-using-rufus)
* Virtualizace povolená v základní desce a [Hyper-V plně povolený](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) v OS Windows 10/11 (vyžaduje restart).
* Povolte spouštění Powershell skriptů na vašem systému - typicky spuštěním `"Set-ExecutionPolicy unrestricted"` v Powershellu spuštěném jako Administrátor.

### Instrukce

1.  Ujistěte se, že váš systém splňuje předpoklady.
2.  [Stáhněte repozitář a extrahujte ho.](https://github.com/jamesstringerparsec/Easy-GPU-PV/archive/refs/heads/main.zip)
3.  Vyhledejte ve svém systému Powershell ISE a spusťte jej jako Administrátor.
4.  V extrahované složce, kterou jste stáhli, otevřete `PreChecks.ps1` v Powershell ISE. Spouštějte soubory zevnitř extrahované složky. Nepřesouvejte je.
5.  Otevřete a Spusťte `PreChecks.ps1` v Powershell ISE pomocí zeleného tlačítka "play" a zkopírujte si název uvedené GPU (nebo varování, která musíte opravit).
6.  Otevřete `CopyFilesToVM.ps1` v Powershell ISE a upravte sekci `params` na začátku souboru. Musíte být opatrní, kolik RAM, úložiště a místa na disku mu přidělíte, váš systém musí mít tyto prostředky k dispozici. Na Windows 10 musí být `GPUName` ponecháno jako `"AUTO"`, na Windows 11 to může být buď `"AUTO"` nebo konkrétní název GPU, kterou chcete rozdělit, přesně tak, jak se objevuje v `PreChecks.ps1`. Navíc musíte poskytnout cestu k ISO souboru Windows 10/11, který jste stáhli.
7.  Spusťte `CopyFilesToVM.ps1` s vašimi změnami v sekci `params` - to může trvat 5-10 minut.
8.  Stáhněte si nástroj pro vzdálenou plochu / streamování her založený na GPU a také ovladač virtuálního displeje (není součástí), abyste plně využili akcelerovanou plochu GPU. Záložka "Issues" (Problémy) v tomto repozitáři má spoustu dobrých nápadů a rad, jak z vašeho nového VM dostat to nejlepší.
9.  Měli byste být připraveni!

### Aktualizace ovladačů GPU, když aktualizujete ovladače na hostiteli

Je důležité aktualizovat ovladače GPU ve VM poté, co jste aktualizovali ovladače GPU na hostiteli. Můžete to udělat takto...

1.  Restartujte hostitele po aktualizaci ovladačů GPU.
2.  Otevřete Powershell jako administrátor a změňte adresář (CD) na cestu, kde se nachází `CopyFilesToVM.ps1` a `Update-VMGpuPartitionDriver.ps1`.
3.  Spusťte `Update-VMGpuPartitionDriver.ps1 -VMName "Jméno vašeho VM" -GPUName "Jméno vaší GPU"` (na Windows 10 musí být jméno GPU `"AUTO"`)

### Hodnoty

`VMName = "GPUP"` - Jméno VM v Hyper-V a název počítače / hostname

`SourcePath = "C:\Users\james\Downloads\Win11_English_x64.iso"` - cesta k ISO Windows 10/11 na vašem hostiteli

`Edition = 6` - Ponechte 6, znamená to Windows 10/11 Pro

`VhdFormat = "VHDX"` - Tuto hodnotu neměňte

`DiskLayout = "UEFI"` - Tuto hodnotu neměňte

`SizeBytes = 40gb` - Velikost disku, v tomto případě 40GB, minimum je 20GB

`MemoryAmount = 8GB` - Velikost paměti, v tomto případě 8GB

`CPUCores = 4` - Počet jader CPU, které chcete dát VM, v tomto případě 4

`NetworkSwitch = "Default Switch"` - Neměňte, pokud nepoužíváte jiný než výchozí Hyper-V Switch

`VHDPath = "C:\Users\Public\Documents\Hyper-V\Virtual Hard Disks\"` - Cesta ke složce, kam chcete uložit disk VM, musí již existovat

`UnattendPath = "$PSScriptRoot"+"\autounattend.xml"` - Tuto hodnotu neměňte

`GPUName = "AUTO"` - AUTO vybere první dostupnou GPU. Na Windows 11 můžete také použít přesný název GPU, kterou chcete sdílet s VM, v situacích s více GPU (výběr GPU není dostupný ve Windows 10 a musí být nastaven na AUTO)

`GPUResourceAllocationPercentage = 50` - Procento GPU, které chcete sdílet s VM

`Username = "GPUVM"` - Uživatelské jméno Windows ve VM, nesmí obsahovat speciální znaky a musí se lišit od hodnoty `VMName`, kterou jste nastavili

`Password = "CoolestPassword!"` - Heslo Windows ve VM, nemůže být prázdné

`Autologon = "true"`- Pokud chcete, aby se VM automaticky přihlásilo na plochu Windows

### Poděkování:

* [Hyper-ConvertImage](https://github.com/tabs-not-spaces/Hyper-ConvertImage) za vytvoření aktualizované verze [Convert-WindowsImage](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/master/hyperv-tools/Convert-WindowsImage), která je kompatibilní s Windows 10 a 11.
* [gawainXX](https://github.com/gawainXX) za pomoc s testováním a upozorňováním na chyby a vylepšení funkcí.

### Poznámky:

* Pro připojení k VM vždy používejte aplikaci pro vzdálenou plochu / streamování her akcelerovanou přes GPU. Ponechte video adaptér Microsoft Hyper-V zakázaný. Používání RDP a režimu "Enhanced Session" (Rozšířená relace) v Hyper-V bude mít za následek nefunkční chování a černé obrazovky ve vaší akcelerované vzdálené ploše. RDP a video adaptér Hyper-V nabízejí maximálně 30 FPS. Použití GPU akcelerovaných vzdálených ploch + ovladače virtuálního displeje vám umožní používat až 4k 60 FPS.
* Pokud dostanete chybu `"ERROR : Cannot bind argument to parameter 'Path' because it is null."`, pravděpodobně to znamená, že jste použili Media Creation Tool ke stažení ISO. To bohužel nemůžete použít, pokud nevidíte přímý odkaz na stažení ISO na stránce Microsoftu, postupujte podle [tohoto průvodce.](https://www.nextofwindows.com/downloading-windows-10-iso-images-using-rufus)
* Vaše GPU na hostiteli bude mít ve Správci zařízení ovladač od Microsoftu, nikoli ovladač od nvidie/intelu/amd. Dokud nad zařízením ve Správci zařízení není žlutý trojúhelník, funguje správně.
* Do GPU musí být zapojen zapnutý displej / HDMI "dummy" dongle, aby vaše aplikace pro vzdálenou plochu / streamování her mohla zachytit obrazovku. Potřebujete pouze jeden na hostitelský stroj bez ohledu na počet VM.
* Vykreslovač Vulkan není dostupný a GL hry mohou nebo nemusí fungovat. [Toto](https://www.microsoft.com/en-us/p/opencl-and-opengl-compatibility-pack/9nqpsl29bfff?SilentAuth=1&wa=wsignin1.0#activetab=pivot:overviewtab) může pomoci s některými OpenGL aplikacemi.
* Pokud na stroji nemáte oprávnění správce, znamená to, že jste nastavili stejné uživatelské jméno a název VM (`username` a `vmname`), tyto hodnoty se musí lišit.
* GPU AMD Polaris, jako je RX 580, v tuto chvíli nepodporují hardwarové kódování videa přes GPU Paravirtualizaci.
* Pro stahování Windows ISO pomocí Rufusu musí mít povoleno "Check for updates" (Zkontrolovat aktualizace).
