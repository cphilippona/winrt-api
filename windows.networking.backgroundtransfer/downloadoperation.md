---
-api-id: T:Windows.Networking.BackgroundTransfer.DownloadOperation
-api-type: winrt class
---

<!-- Class syntax.
public class DownloadOperation : Windows.Networking.BackgroundTransfer.IBackgroundTransferOperation, Windows.Networking.BackgroundTransfer.IBackgroundTransferOperationPriority, Windows.Networking.BackgroundTransfer.IDownloadOperation, Windows.Networking.BackgroundTransfer.IDownloadOperation2
-->

# Windows.Networking.BackgroundTransfer.DownloadOperation

## -description
Performs an asynchronous download operation. The [Background Transfer  sample](http://go.microsoft.com/fwlink/p/?linkid=245064) demonstrates this functionality. For an overview of Background Transfer capabilities, see [Transferring data in the background](http://msdn.microsoft.com/library/9e2ed5b4-af57-456a-884f-1e1d2136a8e8). Download the [Background Transfer sample](http://go.microsoft.com/fwlink/p/?linkid=245064) for examples in JavaScript, C#, and C++.

## -remarks
After app termination, an app should enumerate all existing [DownloadOperation](downloadoperation.md) instances at next start-up using [GetCurrentDownloadsAsync](backgrounddownloader_getcurrentdownloadsasync.md). When a UWP app using Background Transfer is terminated, incomplete downloads will persist in the background. If the app is restarted after termination and these incomplete operations are not enumerated and re-introduced to the current session, they will go stale and continue to occupy device resources.

Background transfer doesn't support concurrent downloads of the same [Uri](../windows.foundation/uri.md). So an app can download *http://example.com/myfile.wmv* once, or download it again after a previous download completed. An app shouldn't start two downloads of the same [Uri](../windows.foundation/uri.md) concurrently, since this may result in truncated files.

> [!NOTE]
> Paused or incomplete download operations can only be resumed if the server accepts range-requests.

**Timeout considerations**


+ 1.) When establishing a new connection for a download over TCP/SSL, the connection attempt is aborted if not established within five minutes.
+ 2.) After the connection has been established, an HTTP request message that has not received a response within two minutes is aborted.
Assuming there is Internet connectivity, Background Transfer will retry a download up to three times. In the event Internet connectivity is not detected, additional attempts will not be made until it is.

**Debugging Guidance**

Stopping a debugging session in Microsoft Visual Studio is comparable to closing your app; downloads are paused and POST uploads are terminated. Even while debugging, your app should enumerate and then pause, resume, restart, or cancel any persisted downloads.

However, if Microsoft Visual Studio project updates, like changes to the app manifest, require that the app is uninstalled and re-deployed for debugging, [GetCurrentDownloadsAsync](backgrounddownloader_getcurrentdownloadsasync.md) cannot enumerate persisted operations created using the previous app deployment.

## -examples
The following example demonstrates how to configure and begin a basic download operation, and is based on the [Background Transfer sample](http://go.microsoft.com/fwlink/p/?linkid=245064) offered in the Windows Sample Gallery.

```javascript
        var download = null;
        var promise = null;

        function DownloadFile (uriString, fileName) {
            try {
                // Asynchronously create the file in the pictures folder.
                Windows.Storage.KnownFolders.picturesLibrary.createFileAsync(fileName, Windows.Storage.CreationCollisionOption.generateUniqueName).done(function (newFile) {
                    var uri = Windows.Foundation.Uri(uriString);
                    var downloader = new Windows.Networking.BackgroundTransfer.BackgroundDownloader();

                    // Create a new download operation.
                    download = downloader.createDownload(uri, newFile);

                    // Start the download and persist the promise to be able to cancel the download.
                    promise = download.startAsync().then(complete, error, progress);
                }, error);
            } catch (err) {
                displayException(err);
            }
        };
```

```csharp
       using Windows.Foundation;
       using Windows.Networking.BackgroundTransfer;
       using Windows.Storage;

       private async void StartDownload_Click(object sender, RoutedEventArgs e)
        {
            try
            {
                Uri source = new Uri(serverAddressField.Text.Trim());
                string destination = fileNameField.Text.Trim();

                StorageFile destinationFile = await KnownFolders.PicturesLibrary.CreateFileAsync(
                    destination, CreationCollisionOption.GenerateUniqueName);

                BackgroundDownloader downloader = new BackgroundDownloader();
                DownloadOperation download = downloader.CreateDownload(source, destinationFile);

                // Attach progress and completion handlers.
                HandleDownloadAsync(download, true);
            }
            catch (Exception ex)
            {
                LogException("Download Error", ex);
            }
        }
```



## -see-also
[Quickstart: Download a file](http://msdn.microsoft.com/library/f7b1a3a0-87b8-4c85-a2a3-be9ff7f04d53), [Background Transfer sample](http://go.microsoft.com/fwlink/p/?linkid=245064)

## -capabilities
internetClient, internetClientServer, privateNetworkClientServer
