## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Compile and Minify for Production

```sh
npm run build
```

### Progress
'''
12/15 - 12/16: Setup project, tested out functionality, noted down areas of improvement

12/17: Finished converting from Vue2 to Vue3

12/18: Finished implementing project specs including pagination and color-coding. Began working on cleaning up Table.vue code to be more readable and set up different components.

12/19: Finished filtering code to allow for easy searching functionality.

'''

### Decisions
'''
I didn't choose to use any new technologies (outside of Vue3) because I didn't believe we needed to complicate the project. I considered using Tailwind CSS because it makes styling much simpler however I felt that it would make the template portions very hard to read (especially considering how Vue3 renders the template).

I decided to break down the components into what made the most sense to me. We needed a hide function (which was used to hide certain data), a search function (which was used to filter on different parameters), a table (which was meant to display the data), and also a paging component that allowed the user to move through data in a much more readable fashion. This led me to create four components that integrated with table.vue to create the project. I decided not to modularize the functions in table.vue further because I wanted a single source of truth without having to worry about passing more props through table and then through the functions. However that definitely could be a future improvement to be made on modularizing the code. 

There were a lot of challenges that I faced in this project. For one, I've never used Vue extensively in the past. It was the first time learning to work with this technology so I had to spend a lot of time learning about Vue3 and how the composition API worked. The pagination component also proved to be more difficult than I imagined. It took a bit of design work to figure out how to work with 0-based indexing while also ensuring the user would see the correct numbers. 

One decision I chose to make is that instead of writing a separate table component for the search function, I chose to reuse the MainTable code by structuring the filteredDisplayData into an object that was readable by MainTable. This saved me from having to write a good amount of code and also streamlined the process. 

The most difficult part was definitely working in the ranges on the filter function. I ran into many bugs with null and undefined and it took a lot of console debugging to figure out where I was going wrong. 
'''

### Bonus Features
'''
I wanted to make the search function as user friendly as possible. Each of the parameters are labeled by their names and there are two buttons that can be used to filter on the given parameters and reset to display the regular data once you are finished. Instead of displaying information in rows of 100, I decided to simply lay it all out in the table. The rationale behind this is if individuals are searching for specific information, they should not have to have over 100 rows of data. 

What could make this even better in the future is if we incorpoated some NLP into this. If we were able to say "Please find me a product that was Launched, is named Core i3-1110G4...", this would make the functionality far more powerful. 
'''
### Creativity and Initiative
'''
In my opinion, the most important part of data visualization tools are that they work. The original tooling already worked very well however it was a little unclear how certain functions worked. I decided to be more explicit with the naming (Hide Function/Search Function) so it would be more obvious what they were meant to do. In addition I chose to have All statuses in its own row (which symbolizes that it would hide all the statuses). In addition, I chose to add a slight green theme to the site. It makes it a bit easier to use as it highlights the key buttons that are supposed to be used on the screen. In addition I added a page number textbox to indicate which page we were on with the data. All in all I think these improvements have made the application more usable. 
'''





