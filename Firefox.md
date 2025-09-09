Vi vill inte se popup om att det finns uppdatering osv, samt att tvinga den att använda så lite minne som möjligt: 
```bash
sudo mkdir -p /usr/lib/firefox/distribution/

sudo nano /usr/lib/firefox/distribution/policies.json

{
  "policies": {
    "DisableFirefoxAccounts": true,
    "DisableProfileImport": true,
    "DisableSetDesktopBackground": true,
    "OverrideFirstRunPage": "",
    "OverridePostUpdatePage": "",
    "Preferences": {
      "dom.ipc.processCount": {
        "Value": 2,
        "Status": "locked"
      },
      "browser.tabs.unloadOnLowMemory": {
        "Value": true,
        "Status": "locked"
      },
      "browser.sessionhistory.max_entries": {
        "Value": 1,
        "Status": "locked"
      },
      "browser.cache.memory.capacity": {
        "Value": 65536,
        "Status": "locked"
      },
      "browser.sessionhistory.max_entries": {
        "Value": 1,
        "Status": "locked"
      }
    }
  }
}
```