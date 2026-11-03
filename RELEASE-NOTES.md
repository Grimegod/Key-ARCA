# Key-ARCA 0.2.2 — language preference reliability

Your language choice is now saved in your Windows user profile instead of the
application folder. It is remembered when the application folder is read-only
and when a newer portable release is extracted into another folder.

An existing language.txt beside the executable is migrated if no valid per-user
preference exists. That legacy file is left untouched. Invalid or oversized
preference files fall back safely; an unavailable profile does not prevent the
application from opening. Saves replace the preference only after the new
value has been written. English remains the first-run default and all eleven
languages are retained.

The preference is stored under %LOCALAPPDATA%/Key-ARCA/Preferences/language.txt.
This contains only the language code and is never transmitted. Copies of the
application launched by the same Windows user share this preference.

## Download and use

Download **Key-ARCA-0.2.2-win-x64.zip** from this release's Assets, extract all
files and run **Key-ARCA.exe**. Keep the extracted files together. This is a
portable Windows x64 package with .NET included. To update, extract the new
release into a separate folder. SHA256SUMS.txt contains the Windows ZIP checksum.

## Preview status

This remains an unsigned freeware preview for Windows 10/11 x64. ZIP, 7z and
ARCA v1 compatibility, encryption and archive safety protections are unchanged.
The ARCA format remains experimental and does not consistently compress better
than optimized 7z. RAR/ISO extraction, split/join, damaged-archive repair and a
Windows installer are not included in this version. The application has not
been obfuscated and has no automatic updater.

Original application source remains proprietary and private. Required
third-party notices and corresponding sources are included under licenses.

Contact: [FFFNewsNow@gmail.com](mailto:FFFNewsNow@gmail.com).
