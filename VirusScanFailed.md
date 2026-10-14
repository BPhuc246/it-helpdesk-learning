# How I Fixed the “Virus Scan Failed” Error When Downloading Files or Images

I started experiencing the **“Virus scan failed”** error whenever I tried to download files or images.

Before this happened, my laptop had a BSOD (Blue Screen of Death) with a notification saying something like, “Your device ran into a problem and needs to restart.” My laptop restarted three times but couldn't boot into Windows properly.

I tried shutting it down and waiting for about 10 minutes before turning it on again. However, it was still stuck in a blue-screen-and-restart loop. Eventually, I somehow managed to get back into Windows.

When I tried downloading an image, the **“Virus scan failed”** error appeared repeatedly.

I'm not a computer expert, but here's what I tried to fix the issue:

### 1. Restart the laptop

I restarted my laptop to see if the problem would go away.

### 2. Try a different browser

I tried downloading files using another browser, but the issue persisted.

### 3. Edit the `ScanAntiVirus` value in Registry Editor

I modified the `ScanAntiVirus` value in the Windows Registry to troubleshoot the download issue.

### 4. Repair and update Microsoft Defender

This was the step that finally worked for me!

I opened **Command Prompt as Administrator** and ran the following commands:

```cmd
cd /d C:\Windows\System32
"%ProgramData%\Microsoft\Windows Defender\Platform\4.18.26090.9-0\MpCmdRun.exe" -SignatureUpdate -MMPC
```

The command updates Microsoft Defender's security intelligence using Microsoft's update source.

**Result:** `Signature update finished.`

After updating Defender, I was able to download files and images successfully without seeing the “Virus scan failed” error.

I hope this helps anyone experiencing the same issue!
