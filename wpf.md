# wpf
> wpf和kinect组合开发模式

# wpf之MVVM
我们使用模式，一般是想达到高内聚低耦合。在WPF开发中，经典的编程模式是MVVM，是为WPF量身定做的模式，该模式充分利用了WPF的数据绑定机制，最大限度地降低了Xmal文件和CS文件的耦合度，也就是UI显示和逻辑代码的耦合度，如需要更换界面时，逻辑代码修改很少，甚至不用修改。与WinForm开发相比，我们一般在后置代码中会使用控件的名字来操作控件的属性来更新UI，而在WPF中通常是通过数据绑定来更新UI；在响应用户操作上，WinForm是通过控件的事件来处理，而WPF可以使用命令绑定的方式来处理，耦合度将降低。

　　我们可以通过下图来直观的理解MVVM模式：
　　![](http://pic002.cnblogs.com/images/2012/466574/2012111410245371.png)

　View就是用xaml实现的界面，负责与用户交互，接收用户输入，把数据展现给用户。
　　ViewModel，一个C#类，负责收集需要绑定的数据和命令，聚合Model对象，通过View类的DataContext属性绑定到View，同时也可以处理一些UI逻辑。
　　Model，就是系统中的对象，可包含属性和行为。

　　一般，View对应一个ViewModel，ViewModel可以聚合N个Model，ViewModel可以对应多个View,Model不知道View和ViewModel的存在。

## `代码实现`
1. 首先定义NotificationObject类。目的是绑定数据属性。这个类的作用是实现了INotifyPropertyChanged接口。WPF中类要实现这个接口，其属性成员才具备通知UI的能力，数据绑定的知识，后面详细讨论。

```

using System.ComponentModel;

namespace WpfFirst
{
    class NotificationObject : INotifyPropertyChanged
    {
        public event PropertyChangedEventHandler PropertyChanged;

        public void RaisePropertyChanged(string propertyName)
        {
            if (this.PropertyChanged != null)
            {
                this.PropertyChanged(this, new PropertyChangedEventArgs(propertyName));
            }
        }
    }
}

```



2. 定义DelegateCommand类。目的是绑定命令属性。这个类的作用是实现了ICommand接口，WPF中实现了ICommand接口的类，才能作为命令绑定到UI。命令的知识，后面详细讨论。

```
using System;
using System.Collections.Generic;
using System.Windows.Input;

namespace WpfFirst
{
    class DelegateCommand : ICommand
    {
        //A method prototype without return value.
        public Action<object> ExecuteCommand = null;
        //A method prototype return a bool type.
        public Func<object, bool> CanExecuteCommand = null;
        public event EventHandler CanExecuteChanged;

        public bool CanExecute(object parameter)
        {
            if (CanExecuteCommand != null)
            {
                return this.CanExecuteCommand(parameter);
            }
            else
            {
                return true;
            }
        }

        public void Execute(object parameter)
        {
            if (this.ExecuteCommand != null) this.ExecuteCommand(parameter);
        }

        public void RaiseCanExecuteChanged()
        {
            if (CanExecuteChanged != null)
            {
                CanExecuteChanged(this, EventArgs.Empty);
            }
        }
    }
}

```

3.开始定义Model类。一个属性成员"WPF",它就是数据属性，的有通知功能，它改变后，会知道通知UI更新。一个方法“Copy”,用来改变属性“WPF”的值，它通过命令的方式相应UI事件。

```

using System.ComponentModel;
using System.Windows.Input;

namespace WpfFirst
{
    class Model : NotificationObject
    {
        private string _wpf = "WPF";

        public string WPF
        {
            get { return _wpf; }
            set
            {
                _wpf = value;
                this.RaisePropertyChanged("WPF");
            }
        }

        public void Copy(object obj)
        {
            this.WPF += " WPF";
        }

    }
}

```
　4.定义ViewModel类。定义了一个命令属性"CopyCmd"，聚合了一个Model对象"model"。这里的关键是，给CopyCmd命令指定响应命令的方法是model对象的“Copy”方法。

```

using System;
namespace WpfFirst
{
    class ViewModel
    {
        public DelegateCommand CopyCmd { get; set; }
        public Model model { get; set; }

        public ViewModel()
        {
            this.model = new Model();
            this.CopyCmd = new DelegateCommand();
            this.CopyCmd.ExecuteCommand = new Action<object>(this.model.Copy);
        }
    }
}

```
　5.定义View.

　　MainWindow.xaml代码：我们能看到，TextBlock控件的text属性，绑定在model对象的WPF属性上; Button的click事件通过命令绑定到CopyCmd命令属性。



```
<Window x:Class="WpfFirst.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MainWindow" Height="350" Width="525">
    <Grid>
        <StackPanel VerticalAlignment="Center" >
            <TextBlock Text="{Binding model.WPF}" Height="208" TextWrapping="WrapWithOverflow"></TextBlock>
            <Button Command="{Binding CopyCmd}" Height="93" Width="232">Copy</Button>
        </StackPanel>
    </Grid>
</Window>

```


　MainWindow.xaml.cs代码:它的工作知识把ViewModel对象赋值到DataContext属性，指定View的数据源就是这个ViewModel。

```

using System.Windows;

namespace WpfFirst
{
    /// <summary>
    /// Interaction logic for MainWindow.xaml
    /// </summary>
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();

            this.DataContext = new ViewModel();
        }
    }
}


```
　6.运行结果。每当我们点击按钮，界面就是被更新了，因为Copy方法改变了WFP属性的值。

![](http://pic002.cnblogs.com/images/2012/466574/2012111411032878.png)
