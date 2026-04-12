notes and syntax of android studio 




            ///there are five types of button created down below
            IconButton(onPressed: (){}, icon: Text("hello")),
            FloatingActionButton(onPressed: (){})
            ,
            OutlinedButton(onPressed: (){}, child: Text("outline")),
            ElevatedButton(
              onPressed: _changeColor,
              child: const Text("Change Color"),
            ),
            TextButton(onPressed: (){}, child: Text("Text")),TextButton.icon(onPressed: (){}, label: CircleAvatar()),
