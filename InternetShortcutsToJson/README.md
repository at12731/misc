# Goal

Get url of internet shortcuts `.url` in the target location on Windows with PowerShell.

# Result

Generate a JSON file with all urls according to folder structure of target location.

# Example

```
.
│   Google.url
│
└───Sub-dir
    │   Microsoft – Cloud, Computers, Apps & Gaming.url
    │
    └───Sub-sub-dir
            Apple.url
```

Result:

```json
{
  "CURRENT_DIR": [
    "https://www.google.com.hk/"
  ],
  "Sub-dir": {
    "Sub-sub-dir": {
      "CURRENT_DIR": [
        "https://www.apple.com/"
      ]
    },
    "CURRENT_DIR": [
      "https://www.microsoft.com/en-us/"
    ]
  }
}
```

# Note

## Pipeline

The below code can be simplified to one line with using pipeline:

```powershell
$result = Get-UrlsHierarchically(Get-Item $targetDir)
ConvertTo-Json $result -Depth $depth > (Join-Path $outputDir "UrlsResult.json")
```

To:

```powershell
Get-UrlsHierarchically(Get-Item $targetDir) | ConvertTo-Json -Depth $depth > (Join-Path $outputDir "UrlsResult.json")
```

However, PowerShell prompt this warning when the depth of directory is greater than 1:

```
WARNING: Resulting JSON is truncated as serialization has exceeded the set depth of 1.
```

It is because pipeline capture the variable `$depth` immediately before the function `Get-UrlsHierarchically` update the `$depth`. It means the value of `$depth` still is the initial value `1` when pass to cmdlet `ConvertTo-Json`.

## Variable Scope

PowerShell is using parent and child scope to distinguish the variable scope instead of block level. It means the variable scope is affected by function and script but not for-block nor if-block etc.

For a function need to access outside variable in the same script, use scope modifier `script:` (eg `$script:depth`)

For more information, [about Scopes - PowerShell | Microsoft Docs](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_scopes?view=powershell-7.1#parent-and-child-scopes)

# Reference

- [Get target of shortcut (.lnk) file with powershell - Stack Overflow](https://stackoverflow.com/questions/42762122/get-target-of-shortcut-lnk-file-with-powershell)

- [powershell - Unexpected ConvertTo-Json results? Answer: it has a default -Depth of 2 - Stack Overflow](https://stackoverflow.com/questions/53583677/unexpected-convertto-json-results-answer-it-has-a-default-depth-of-2)
