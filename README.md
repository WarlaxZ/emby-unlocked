# emby-unlocked
Version: **Emby 3.2.31.0**

Emby with the premium Emby Premiere features unlocked.

## Releases
Releases including the patch are available below:

- [Arch Linux](https://aur.archlinux.org/packages/emby-server-unlocked/)
- [Docker](https://hub.docker.com/r/nvllsvm/emby-unlocked/)

## Why?
At some point, the developers of Emby added a nasty nag screen before playback.
Worse, you need to wait a few seconds before being able to dismiss it.
They refuse to address removing it as seen [here](https://github.com/MediaBrowser/Emby/issues/2469).

This is **bullshit** for a GPLv2 licensed product. Of course someone is going to fork Emby to remove it.

So while my main motivation was to **remove** the nag screen, unlocking all the premium features proved the simplest way.

## Help! Premiere feature x does not work.
While this patch makes your local server believe Emby Premiere features are unlocked, some features may still not function.
For example, both tv.emby.media and the mobile apps rely upon validation with the Emby-owned mb3admin.com server.

## Modifications

[PluginSecurityManager.cs.patch](https://github.com/nvllsvm/emby-unlocked/blob/master/patches/PluginSecurityManager.cs.patch)

Before compilation, simply patch the existing file:
```
patch -N -p1 -r - Emby.Server.Implementations/Security/PluginSecurityManager.cs < ../PluginSecurityManager.cs.patch
```

[connectionmanager.js](https://github.com/nvllsvm/emby-unlocked/blob/master/replacements/connectionmanager.js)

Not really sure what this unlocks outside of removing the nag on the **Sync** screen.
Sync doesn't seem to work afterwards. 
Regardless - your own Emby server has zero need to contact the Emby-owned validation URL: [https://mb3admin.com/admin/service/registration/validateDevice](https://mb3admin.com/admin/service/registration/validateDevice).

The included version of this in the source distribution is minified. Thus, making a patch is difficult.
The only difference boils down to replacing ``self.getRegistrationInfo`` with this:

```
self.getRegistrationInfo = function(feature, apiClient) {
    var cacheKey = "regInfo-" + apiClient.serverInfo().Id;
    appStorage.setItem(cacheKey, JSON.stringify({
        lastValidDate: new Date().getTime(),
        deviceId: self.deviceId()
    }));
    return Promise.resolve();
}
```
