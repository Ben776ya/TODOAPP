
# The model/view architecture
##  Overview
_**Content Taken from**  [Here](https://doc.qt.io/qt-5/model-view-programming.html)_


The model communicates with a source of data, providing an  _interface_  for the other components in the architecture. The nature of the communication depends on the type of data source, and the way the model is implemented.

The view obtains  _model indexes_  from the model; these are references to items of data. By supplying model indexes to the model, the view can retrieve items of data from the data source.

In standard views, a  _delegate_  renders the items of data. When an item is edited, the delegate communicates with the model directly using model indexes.

![mainwindowlayout](https://doc.qt.io/qt-5/images/modelview-overview.png)  

  **Note:** Models, views, and delegates communicate with each other using _signals and slots_.
  

 
  # To Do App
  ## Context
  
 A to-do list app **lets you write, organize, and reprioritize your tasks more efficiently**. They also let you attach notes, links, and files to a task, and many let you see when someone else has completed a task. In many ways, a good to-do app is the ultimate productivity app.
  
 We will try to make such a tool ourselves .
 
 -  Creating a From using  [QtListView](https://doc.qt.io/qt-5/qlistview.html)
 
 ![enter image description here](https://cdn.discordapp.com/attachments/934852496709529650/934856942864728064/1.PNG)

- Add this code to our header
  ```cpp
    QStringListModel *model1;
    QStringListModel *model2;
    QStringListModel *model3;
    
    QStringList Todaytasks;
    QStringList Finishedtasks;
    QStringList Pendingtasks;
  ```
  ```cpp
    model1 = new QStringListModel();
    model2 = new QStringListModel();
    model3 = new QStringListModel();
    ```
    - Setting the model to the view
     ```cpp
     model1->setStringList(Todaytasks);
     ui->lw1->setModel(model1);


     model2->setStringList(Pendingtasks);
     ui->lw2->setModel(model2);


     model3->setStringList(Finishedtasks);
     ui->lw3->setModel(model3);
  ```
  -  The add action function

```cpp
 void ToDoApp::on_action_Add_triggered()
 {
     addTaskDialog dialog;
     auto reply= dialog.exec();
     if(reply == addTaskDialog::Accepted)
     {
         QString text= dialog.getText();
         if(dialog.getDate()==QDate::currentDate() && !dialog.isChecked()){
             Todaytasks.append(text);
         }else if(dialog.getDate()!=QDate::currentDate() && !dialog.isChecked()){
             Pendingtasks.append(text);
         }else if(dialog.isChecked()){
             Finishedtasks.append(text);
         }
     }

     model1->setStringList(Todaytasks);
     ui->lw1->setModel(model1);

     model2->setStringList(Pendingtasks);
     ui->lw2->setModel(model2);


     model3->setStringList(Finishedtasks);
     ui->lw3->setModel(model3);
 }
 ```
  - Adding the menu tools Functions ( new , open , save )
  
  - Save :
   ```cpp
   void ToDoApp::closeEvent(QCloseEvent* e){
    QFile file("C:/Users/Hamza ess/Desktop/ez.txt");
    if(file.open(QIODevice::ReadWrite | QIODevice::Text)){
        QTextStream out(&file);
        for(int i=0;i<Todaytasks.size();i++)
        {
            out << "1"<< Todaytasks.at(i)<< Qt::endl;
        }
        for(int i=0;i<Pendingtasks.size();i++)
        {
            out << "2"<< Pendingtasks.at(i) << Qt::endl;
        }
        for(int i=0;i<Finishedtasks.size();i++)
        {
            out << "3"<< Finishedtasks.at(i) << Qt::endl;
        }
        file.close();
    }
   }
   ```
   - Open :
     - Implementing the code to the constructor
   
   ```cpp
      QFile file("C:/Users/zakariae zaoui/Desktop/alo.txt");

      if (!file.open(QIODevice::ReadOnly | QIODevice::Text))
            return;

      while (!file.atEnd()) {
          QString line = file.readLine();
          if(line.at(0)=="1"){
                Todaytasks.append(line.mid(1,line.size()));
          }else if(line.at(0)=="2"){
              Pendingtasks.append(line.mid(1,line.size()));
          }else if(line.at(0)=="3"){
              Finishedtasks.append(line.mid(1,line.size()));
          }
      }
      
      model1->setStringList(Todaytasks);
      ui->lw1->setModel(model1);

      model2->setStringList(Pendingtasks);
      ui->lw2->setModel(model2);

      model3->setStringList(Finishedtasks);
      ui->lw3->setModel(model3);
   ```
   - To modify a task´s attributes first we need to connect the lists to the Slots that are going to perform these actions
   ```cpp
      connect(ui->lw1, SIGNAL(doubleClicked(QModelIndex)), this,SLOT(sss()));
      connect(ui->lw2, SIGNAL(doubleClicked(QModelIndex)), this,SLOT(sss2()));
      connect(ui->lw3, SIGNAL(doubleClicked(QModelIndex)), this,SLOT(sss3()));
   ```
   - the implementation of sss()
   ```cpp
   void ToDoApp::sss(){
    addTaskDialog dialog;
    int i=1;
    QString task;
    QModelIndex index=ui->lw1->currentIndex();
    QString a=Todaytasks.at(index.row());
    QStringList list = a.split(QRegularExpression("\\W+"), Qt::SkipEmptyParts);
    task+=list[0];

    while(list[i]!="Due"){
         task+=" "+list[i];
         i++;
    }

    dialog.setdate(list[i+3].toInt(),list[i+1].toInt(),list[i+2].toInt());
    dialog.setdesc(task);
    dialog.settag(list[i+5]);
    auto reply= dialog.exec();


     if(reply == addTaskDialog::Accepted)
     {
         QString text= dialog.getText();
         if(dialog.getDate()==QDate::currentDate() && !dialog.isChecked()){
             Todaytasks.append(text);
             Todaytasks.removeAt(index.row());

         }else if(dialog.getDate()!=QDate::currentDate() && !dialog.isChecked()){
             Pendingtasks.append(text);
             Todaytasks.removeAt(index.row());
         }else if(dialog.isChecked()){
             Finishedtasks.append(text);
             Todaytasks.removeAt(index.row());
         }

     }

     model1->setStringList(Todaytasks);
     ui->lw1->setModel(model1);

     model2->setStringList(Pendingtasks);
     ui->lw2->setModel(model2);


     model3->setStringList(Finishedtasks);
     ui->lw3->setModel(model3);

   } 
   ```
   - the implementation of sss2()
   ```cpp
   void ToDoApp::sss2 (){
    addTaskDialog dialog;
    int i=1;
    QString task;
    QModelIndex index=ui->lw2->currentIndex();
    QString a=Pendingtasks.at(index.row());
    QStringList list = a.split(QRegularExpression("\\W+"), Qt::SkipEmptyParts);
    task+=list[0];
    while(list[i]!="Due"){
         task+=" "+list[i];
         i++;
    }
    dialog.setdate(list[i+3].toInt(),list[i+1].toInt(),list[i+2].toInt());
    dialog.setdesc(task);
    dialog.settag(list[i+5]);
    auto reply= dialog.exec();
    if(reply == addTaskDialog::Accepted){
         QString text= dialog.getText();
         if(dialog.getDate()==QDate::currentDate() && !dialog.isChecked()){
             Todaytasks.append(text);
             Pendingtasks.removeAt(index.row());
         }else if(dialog.getDate()!=QDate::currentDate() && !dialog.isChecked()){
             Pendingtasks.append(text);
   Pendingtasks.removeAt(index.row());
         }else if(dialog.isChecked()){
             Finishedtasks.append(text);
             Pendingtasks.removeAt(index.row());
         }


    }

    model1->setStringList(Todaytasks);
    ui->lw1->setModel(model1);

    model2->setStringList(Pendingtasks);
    ui->lw2->setModel(model2);


    model3->setStringList(Finishedtasks);
    ui->lw3->setModel(model3);
    }
   ```
   - the implementation of sss3()
   ```cpp
    void ToDoApp::sss3(){
    addTaskDialog dialog;
    int i=1;
    QString task;
    QModelIndex index=ui->lw3->currentIndex();
    QString a=Finishedtasks.at(index.row());
    QStringList list = a.split(QRegularExpression("\\W+"), Qt::SkipEmptyParts);
    task+=list[0];
    while(list[i]!="Due"){
         task+=" "+list[i];
         i++;
    }
    dialog.setdate(list[i+3].toInt(),list[i+1].toInt(),list[i+2].toInt());
    dialog.setdesc(task);
    dialog.setf(true);
    dialog.settag(list[i+5]);
    auto reply= dialog.exec();
    if(reply == addTaskDialog::Accepted){
         QString text= dialog.getText();
         if(dialog.getDate()==QDate::currentDate() && !dialog.isChecked()){
             Todaytasks.append(text);
              Finishedtasks.removeAt(index.row());
         }else if(dialog.getDate()!=QDate::currentDate() && !dialog.isChecked()){
             Pendingtasks.append(text);
              Finishedtasks.removeAt(index.row());
         }else if(dialog.isChecked()){

         }

    }

    model1->setStringList(Todaytasks);
    ui->lw1->setModel(model1);

    model2->setStringList(Pendingtasks);
    ui->lw2->setModel(model2);

    model3->setStringList(Finishedtasks);
    ui->lw3->setModel(model3);
    }
   ```
   ## Drag and Drop
first we need to set the drag and drop mode
```cpp
      ui->lw2->setDragDropMode(QAbstractItemView::DragDrop);
      ui->lw1->setDragDropMode(QAbstractItemView::DragDrop);
      ui->lw3->setDragDropMode(QAbstractItemView::DropOnly);
```
we have to make some changes if we drag a task from todays tasks to pending tasks
> and by default we need to add a day

```cpp
connect(model2, SIGNAL(rowsInserted(QModelIndex,int,int)), this,SLOT(drop1()));
```
```cpp
void ToDoApp::drop1(){

    addTaskDialog dialog;
    int i=1;
    QString task;
    QModelIndex index=ui->lw1->currentIndex();
    QString a=Todaytasks.at(index.row());
    QStringList list = a.split(QRegularExpression("\\W+"), Qt::SkipEmptyParts);
    task+=list[0];
    while(list[i]!="Due"){
         task+=" "+list[i];
         i++;
    }
    dialog.setdate(list[i+3].toInt(),list[i+1].toInt(),list[i+2].toInt()+1);
    dialog.setdesc(task);
    dialog.settag(list[i+5]);
    auto reply= dialog.exec();
    if(reply == addTaskDialog::Accepted){
         QString text= dialog.getText();
         if(dialog.getDate()==QDate::currentDate() && !dialog.isChecked()){

         }else if(dialog.getDate()!=QDate::currentDate() && !dialog.isChecked()){
             Pendingtasks.append(text);
             Todaytasks.removeAt(index.row());
         }else if(dialog.isChecked()){
             Finishedtasks.append(text);
             Todaytasks.removeAt(index.row());
         }
    }

    ui->lw2->clearSelection();
    model1->setStringList(Todaytasks);
    ui->lw1->setModel(model1);

    model2->setStringList(Pendingtasks);
    ui->lw2->setModel(model2);


    model3->setStringList(Finishedtasks);
    ui->lw3->setModel(model3);
}
```
Let´s manipulate it a bit



![enter image description here](https://cdn.discordapp.com/attachments/919005818559529022/934866672475578469/150649164-d2122718-3122-465c-ad9e-613dad2459c5.png)

Test it

![Capture d’écran 2022-01-22 182816](https://user-images.githubusercontent.com/85891554/150650997-cb0bef8b-9a0d-4020-9629-143b5f3c363b.png)
## To Do App Based on Item Views

Qt provides a lot of capabalities to display pre- and user-defined item models in different ways. The separation of functionality introduced by the model/view architecture gives developers greater flexibility to customize the presentation of items.
### The application interface :
[Qt Designer](https://doc.qt.io/qt-5/qtdesigner-manual.html) to set our application

![enter image description here](https://cdn.discordapp.com/attachments/934852496709529650/934857028915040256/unknown.png)
### Defining a Task

A Task is defined by the following attributes:
- A description: The task to accomplish

- A check box to indicate weither the task was fullfiled or not
- A Tag category to show the class of the task which is reduced to the following values:

  - Work
  - Life
  - Other
- Finally, a task should have a DueDate which stores the Date planned for the date.

### Create the dialog :

![enter image description here](https://cdn.discordapp.com/attachments/934852496709529650/934857029414158376/unknown.png)

now , we will add setters and a function to get text from the dialog
- date setter 
```cpp
void addTaskDialog::setdate(int y,int m, int d){
    QDate da;

    da.setDate(y,m,d);
    ui->dateEdit->setDate(da);
}
```
- description setter
```cpp
void addTaskDialog::setdesc(QString a){
    ui->lineEdit->setText(a);
}
```
- tag setter
```cpp
void addTaskDialog::settag(QString a){
    ui->comboBox->setCurrentText(a);
}
```
- checkbox setter
```cpp
void addTaskDialog::setf(bool a){
    ui->checkBox->setChecked(a);
}
```
- gettext function
```cpp
QString addTaskDialog::getText(){

    QString a= ui->lineEdit->text() + " Due : " + ui->dateEdit->text() + "  Tag : " + ui->comboBox->currentText() +"";
    return a;
}
```
we need a function to check if the checkbox is checked
```cpp
bool addTaskDialog::isChecked(){
    if(ui->checkBox->isChecked()){
        return true;
    }
    return false;
}
```
we need also to set a minimum date
```cpp
    QDate date =QDate::currentDate();
    ui->dateEdit->setMinimumDate(date);
    ui->dateEdit->setDate(date);
```


![enter image description here](https://cdn.discordapp.com/attachments/934852496709529650/934857029619707924/unknown.png)

## Add task function
first we add a slot to our header
```cpp
private slots :
  void on_action_Add_triggered();
```
now the implementation of our function :
```cpp
     //first we call the task dialog
     addTaskDialog dialog;
     auto reply= dialog.exec();
     if(reply == addTaskDialog::Accepted)
     {
         //we save the task on a text string using the gettext function
         QString text= dialog.getText();
         // now based on the date and the checkbox status we will decide on wich listwidget we are going to add our task as an item
         if(dialog.getDate()==QDate::currentDate() && !dialog.isChecked()){
             ui->listWidget->addItem(text);
         }else if(dialog.getDate()!=QDate::currentDate() && !dialog.isChecked()){
             ui->lw2->addItem(text);
         }else if(dialog.isChecked()){
             ui->lw3->addItem(text);
         }
     }
```
## Hide/show the pending and finished views
The user could either hide/show the pending and finished views
for this we add the following slots to the header
```cpp
private slots:
    void on_action_View_pending_tasks_toggled(bool arg1);
    void on_action_View_finished_tasks_toggled(bool arg1);
```
the implementation of on_action_View_pending_tasks_toggled slot
```cpp
void ToDoApp::on_action_View_pending_tasks_toggled(bool arg1)
{
    if(arg1){
        ui->lw2->setVisible(true);
    }else{
        ui->lw2->setVisible(false);
    }
}
```
the implementation of on_action_View_finished_tasks_toggled slot
```cpp
void ToDoApp::on_action_View_finished_tasks_toggled(bool arg1)
{
    if(arg1){
        ui->lw3->setVisible(true);
    }else{
        ui->lw3->setVisible(false);
    }
}
```
we need to add this code to the constructor to make sure that these widgets are invisible by default
```cpp
   ui->lw2->setVisible(false);
   ui->lw3->setVisible(false);
```
## Save tasks
now we neeed to add a function so we can save our tasks after closing our application
to do this we are going to override a close event
```cpp
protected:
    void closeEvent(QCloseEvent* e) override;
```
we are going to save the tasks on a .txt file
```cpp
void ToDoApp::closeEvent(QCloseEvent* e){
    
    QFile file("C:/Users/zakariae zaoui/Desktop/alo.txt");
    if(file.open(QIODevice::ReadWrite | QIODevice::Text)){
        QTextStream out(&file);
        
        for(int i=0;i<ui->listWidget->count();i++)
        {
            out << "1"<< ui->listWidget->item(i)->text() << Qt::endl;
        }
        for(int i=0;i<ui->lw2->count();i++)
        {
            out << "2"<< ui->lw2->item(i)->text() << Qt::endl;
        }
        for(int i=0;i<ui->lw3->count();i++)
        {
            out << "3"<< ui->lw3->item(i)->text() << Qt::endl;
        }
        file.close();
    }
}
```
## Open tasks
the tasks entered to our application must remains in the app in future use
to do this we need to add this code to the constructor 
```cpp
      QFile file("C:/Users/zakariae zaoui/Desktop/alo.txt");

      if (!file.open(QIODevice::ReadOnly | QIODevice::Text))
            return;
      // by checking the first character of line we can decidde where to add this item
      while (!file.atEnd()) {
          QString line = file.readLine();
          if(line.at(0)=="1"){
                ui->listWidget->addItem(line.mid(1,line.size()));
          }else if(line.at(0)=="2"){
              ui->lw2->addItem(line.mid(1,line.size()));
          }else if(line.at(0)=="3"){
              ui->lw3   ->addItem(line.mid(1,line.size()));
          }
      }
```
## Change the content of an item
First we need to connect the list widget to a function that will be executed if an item is double clicked
```cpp
      connect(ui->listWidget, SIGNAL(itemDoubleClicked(QListWidgetItem*)), this,SLOT(sss()));
      connect(ui->lw2, SIGNAL(itemDoubleClicked(QListWidgetItem*)), this,SLOT(sss2()));
      connect(ui->lw3, SIGNAL(itemDoubleClicked(QListWidgetItem*)), this,SLOT(sss3()));
```
we have to add these three SLOTS to the header
```cpp
private slots:    
    void sss();
    void sss2();
    void sss3();
```
now the implementation of the slots
sss():
```cpp
void ToDoApp::sss(){
    addTaskDialog dialog;
    int i=1;
    
    QString task;
    // declare the new task
    QListWidgetItem *a=ui->listWidget->currentItem();
    // now we split the task 
    QStringList list = a->text().split(QRegularExpression("\\W+"), Qt::SkipEmptyParts);
    task+=list[0];
    
    // this loop is going to store the description on a string named task
    while(list[i]!="Due"){
         task+=" "+list[i];
         i++;
    }
    
    // set the date
    dialog.setdate(list[i+3].toInt(),list[i+1].toInt(),list[i+2].toInt());
    
    // set the description
    dialog.setdesc(task);
    
    //set the tag
    dialog.settag(list[i+5]);
    auto reply= dialog.exec();
    if(reply == addTaskDialog::Accepted)
     {
         QString text= dialog.getText();
         if(dialog.getDate()==QDate::currentDate() && !dialog.isChecked()){
             ui->listWidget->addItem(text);
         }else if(dialog.getDate()!=QDate::currentDate() && !dialog.isChecked()){
             ui->lw2->addItem(text);
         }else if(dialog.isChecked()){
             ui->lw3->addItem(text);
         }
         // delete the previous item so we won't have a duplicated one 
         delete a ;
     }
}
```
same thing for the second slot
the implementation of the third slot is different
we have to change the checkbox status
```cpp
void ToDoApp::sss(){
    addTaskDialog dialog;
    int i=1;
    
    QString task;
    // declare the new task
    QListWidgetItem *a=ui->listWidget->currentItem();
    // now we split the task 
    QStringList list = a->text().split(QRegularExpression("\\W+"), Qt::SkipEmptyParts);
    task+=list[0];
    
    // this loop is going to store the description on a string named task
    while(list[i]!="Due"){
         task+=" "+list[i];
         i++;
    }
    
    // set the date
    dialog.setdate(list[i+3].toInt(),list[i+1].toInt(),list[i+2].toInt());
    
    // set the description
    dialog.setdesc(task);
    
    //set the check box for finished tasks to true
    dialog.setf(true);
    
    //set the tag
    dialog.settag(list[i+5]);
    auto reply= dialog.exec();
    if(reply == addTaskDialog::Accepted)
     {
         QString text= dialog.getText();
         if(dialog.getDate()==QDate::currentDate() && !dialog.isChecked()){
             ui->listWidget->addItem(text);
         }else if(dialog.getDate()!=QDate::currentDate() && !dialog.isChecked()){
             ui->lw2->addItem(text);
         }else if(dialog.isChecked()){
             ui->lw3->addItem(text);
         }
         // delete the previous item so we won't have a duplicated one 
         delete a ;
     }
}
```

# ***TERMINATED***
