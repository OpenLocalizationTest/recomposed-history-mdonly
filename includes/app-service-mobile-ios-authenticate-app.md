**Objective-C**: 

1. On your Mac, open _QSTodoListViewController.m_ in Xcode and add the following method. Change _facebook_ to _microsoftaccount_, _twitter_, _google_, or _windowsazureactivedirectory_ if you're not using Facebook as your identity provider.

            - (void) loginAndGetData
            {
                MSClient *client = self.todoService.client;
                if (client.currentUser != nil) {
                    return;
                }
            
                [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                    [self refresh];
                }];
            }


2. Replace `[self refresh]` in `viewDidLoad` in _QSTodoListViewController.m_ with the following:

            [self loginAndGetData];

3. Press  **Run** to start the app, and then log in. When you are logged in, you should be able to view the Todo list and make updates.

**Swift**:

1. On your Mac, open _ToDoTableViewController.swift_ in Xcode and add the following method. Change _facebook_ to _microsoftaccount_, _twitter_, _google_, or _windowsazureactivedirectory_ if you're not using Facebook as your identity provider. Replace _%APPURL%_ with the URL of the Azure Mobile App.
        
```            
func loginAndGetData()
{
    let client = MSClient(applicationURLString: "%APPURL%")
    if client.currentUser != nil {
        return
    }
        
    client.loginWithProvider("facebook", controller: self, animated: true, completion: { (user, error) -> Void in
        self.refreshControl?.beginRefreshing()
        self.onRefresh(self.refreshControl)
    })
}
```

2. Remove the lines `self.refreshControl?.beginRefreshing()` and `self.onRefresh(self.refreshControl)` at the end of `viewDidLoad()` in _ToDoTableViewController.swift_. Add a call to `loginAndGetData()` in their place:

```
loginAndGetData();
```

3. Press  **Run** to start the app, and then log in. When you are logged in, you should be able to view the Todo list and make updates.
