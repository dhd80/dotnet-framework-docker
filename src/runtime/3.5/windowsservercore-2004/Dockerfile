# escape=`

FROM mcr.microsoft.com/windows/servercore:2004-amd64

# Enable detection of running in a container
ENV DOTNET_RUNNING_IN_CONTAINER=true `
    # Ngen workaround: https://github.com/microsoft/dotnet-framework-docker/issues/231
    COMPLUS_NGenProtectedProcess_FeatureEnabled=0

# Install .NET Fx 3.5
RUN curl -fSLo microsoft-windows-netfx3.zip https://dotnetbinaries.blob.core.windows.net/dockerassets/microsoft-windows-netfx3-2004.zip `
    && tar -zxf microsoft-windows-netfx3.zip `
    && del /F /Q microsoft-windows-netfx3.zip `
    && DISM /Online /Quiet /Add-Package /PackagePath:.\microsoft-windows-netfx3-ondemand-package~31bf3856ad364e35~amd64~~.cab `
    && del microsoft-windows-netfx3-ondemand-package~31bf3856ad364e35~amd64~~.cab `
    && powershell Remove-Item -Force -Recurse ${Env:TEMP}\*

# Apply latest patch
RUN curl -fSLo patch.msu http://download.windowsupdate.com/c/msdownload/update/software/secu/2020/10/windows10.0-kb4578968-x64-ndp48_769c0d9bbb7cdff4adcd235f6064b272d71a23d6.msu `
    && mkdir patch `
    && expand patch.msu patch -F:* `
    && del /F /Q patch.msu `
    && DISM /Online /Quiet /Add-Package /PackagePath:C:\patch\windows10.0-kb4578968-x64-ndp48.cab `
    && rmdir /S /Q patch

RUN `
    # Ngen top of assembly graph to optimize a set of frequently used assemblies
    \Windows\Microsoft.NET\Framework64\v4.0.30319\ngen install "Microsoft.PowerShell.Utility.Activities, Version=3.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" `
    # To optimize 32-bit assemblies, uncomment the next line and add an escape character (`) to the end of the previous line
    # && \Windows\Microsoft.NET\Framework\v4.0.30319\ngen install "Microsoft.PowerShell.Utility.Activities, Version=3.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" `
    && C:\Windows\Microsoft.NET\Framework64\v4.0.30319\ngen.exe update `
    && C:\Windows\Microsoft.NET\Framework\v4.0.30319\ngen.exe update
