---
title: Call Back Hell：Promise誕生
date: 2018-10-02 22:42:10
tags:
---
```
      // first
        function getRecipt(){
          setTimeout( () => {
            const recipeID = [523, 883, 432, 974];
            console.log(recipeID);

            setTimeout( id => {
              const recipt = {
                title: 'Fresh tomato',
                publisher: 'Joans'
              }
              console.log(`${id}: ${recipt.title}`);

              setTimeout(publisher => {
                const recipe = {
                  title: 'Italian Pizza',
                  publisher: 'Joans'
                }
                console.log(recipe.publisher);
              }, 1500, recipt.publisher);
            }, 1000, recipeID[2]);
          }, 1500);
        }
        getRecipt();
```
難管理
為了解決這樣的問題，於是誕生了「Promise」