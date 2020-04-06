# How to load ListView using Rg.plugin.Popup in Xamarin forms ListView (SfListView)?

You can load the Xamarin.Forms [SfListView](https://help.syncfusion.com/xamarin/listview/overview?) inside popup using [Rg.Plugin.Popup](https://github.com/rotorgames/Rg.Plugins.Popup) framework. Please follow the below steps to work with Rg plugin popup.

You can refer the following article.

https://www.syncfusion.com/kb/11353/how-to-show-xamarin-forms-listview-sflistview-in-popup-using-rg-plugin-popup-framework

**Step 1:** Install the [Rg.Plugin.Popup](https://www.nuget.org/packages/Rg.Plugins.Popup) Nuget package in the shared code project.

**Step 2:** Initialize the popup plugin in each platform.

In Android, initialize the plugin in MainActivity.cs page. Also, override the [OnButtonPressed](https://github.com/rotorgames/Rg.Plugins.Popup/wiki/Getting-started#android-back-button) and invoke SendBackPressed for back button to work with the plugin.
``` c#
namespace ListViewXamarin.Droid
{
    [Activity(Label = "ListViewXamarin", Icon = "@mipmap/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        protected override void OnCreate(Bundle savedInstanceState)
        {
            TabLayoutResource = Resource.Layout.Tabbar;
            ToolbarResource = Resource.Layout.Toolbar;
 
            base.OnCreate(savedInstanceState);
            Rg.Plugins.Popup.Popup.Init(this, savedInstanceState);
            global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
            LoadApplication(new App());
        }
        
        public override void OnBackPressed()
        {
            if (Rg.Plugins.Popup.Popup.SendBackPressed(base.OnBackPressed))
            {
                // Do something if there are some pages in the `PopupStack`
            }
            else
            {
                // Do something if there are not any pages in the `PopupStack`
            }
        }
    }
}
```
In iOS, initialize the plugin in AppDelegate.cs page.
``` c#
namespace ListViewXamarin.iOS
{
    [Register("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
            global::Xamarin.Forms.Forms.Init();
            SfListViewRenderer.Init();
            Rg.Plugins.Popup.Popup.Init();
            LoadApplication(new App());
 
            return base.FinishedLaunching(app, options);
        }
    }
}
```
In UWP, initialize the plugin in App.xaml.cs page.
```c#
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
    ...
    if (rootFrame == null)
    {
        // Create a Frame to act as the navigation context and navigate to the first page
        rootFrame = new Frame();
 
        rootFrame.NavigationFailed += OnNavigationFailed;
 
        Rg.Plugins.Popup.Popup.Init();
        Xamarin.Forms.Forms.Init(e);
 
        if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
        {
            //TODO: Load state from previously suspended application
        }
        }
        ...
}
```
**Step 3:** Use command to show the popup using [PopupNavigation](https://github.com/rotorgames/Rg.Plugins.Popup/wiki/Navigation) services in ViewModel class and bind the command to button.
``` c#
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ListViewXamarin"
             x:Class="ListViewXamarin.MyContentPage">
 
    <Button Text="Show Popup" 
            Command="{Binding ShowPopup}" 
            HeightRequest="50" 
            WidthRequest="150"
            HorizontalOptions="Center" 
            VerticalOptions="Center"
            BindingContext="{local:FileManagerViewModel}"/>
</ContentPage>
```
``` c#
public Command ShowPopup { get; set; }
 
public ContactsViewModel()
{
    ShowPopup = new Command(ShowPopupPage);
}
private async void ShowPopupPage(object obj)
{
    await PopupNavigation.Instance.PushAsync(new MainPage());
}
```

**Step 4:** Create new page and add SfListView inside PopupPage.Content.

``` xml
<pages:PopupPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:pages="clr-namespace:Rg.Plugins.Popup.Pages;assembly=Rg.Plugins.Popup"
             xmlns:syncfusion="clr-namespace:Syncfusion.ListView.XForms;assembly=Syncfusion.SfListView.XForms"
             x:Class="ListViewXamarin.ListViewPage">
 
    <pages:PopupPage.BindingContext>
        <local:ContactsViewModel x:Name="vm"/>
    </pages:PopupPage.BindingContext>
 
    <pages:PopupPage.Content>
            <syncfusion:SfListView x:Name="listView" 
                                   AutoFitMode="Height" 
                                   ItemsSource="{Binding contactsinfo}"
                                   Margin="40">
                <syncfusion:SfListView.ItemTemplate >
                    <DataTemplate>
                        <ViewCell>
                            <ViewCell.View>
                                <Grid x:Name="grid" RowSpacing="0">
                                    <Grid.RowDefinitions>
                                        <RowDefinition Height="*" />
                                        <RowDefinition Height="1" />
                                    </Grid.RowDefinitions>
                                    <Grid RowSpacing="0">
                                        <Grid.ColumnDefinitions>
                                            <ColumnDefinition Width="70" />
                                            <ColumnDefinition Width="*" />
                                            <ColumnDefinition Width="Auto" />
                                        </Grid.ColumnDefinitions>
 
                                        <Image Source="{Binding ContactImage}"
                                               HeightRequest="50" WidthRequest="50"/>
                                        <Grid Grid.Column="1">
                                            <Grid.RowDefinitions>
                                                <RowDefinition Height="*" />
                                                <RowDefinition Height="*" />
                                            </Grid.RowDefinitions>
                                        <Label Text="{Binding ContactName}"/>
                                        <Label Grid.Row="1"
                                               Grid.Column="0"
                                               Text="{Binding ContactNumber}"/>
                                        </Grid>
                                            <Grid Grid.Row="0"
                                                  Grid.Column="2">
                                            <Label Text="{Binding ContactType}"/>
                                        </Grid>
                                    </Grid>
                                </Grid>
                            </ViewCell.View>
                        </ViewCell>
                    </DataTemplate>
                </syncfusion:SfListView.ItemTemplate>
            </syncfusion:SfListView>
    </pages:PopupPage.Content>
</pages:PopupPage>
```
**Output**

![ListViewInPopup](https://github.com/SyncfusionExamples/rgplugin-popup-listview-xamarin/blob/master/ScreenShots/ListViewinPopup.png)
