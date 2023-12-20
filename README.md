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

12/15 - 12/16: Setup project, tested out functionality, noted down areas of improvement

12/17: Finished converting from Vue2 to Vue3

12/18: Finished implementing project specs including pagination and color-coding. Began working on cleaning up Table.vue code to be more readable and set up different components.

12/19: Finished filtering code to allow for easy searching functionality.


### Decisions

I made deliberate choices regarding the use of new technologies in this project, opting not to introduce any besides Vue 3. My reasoning was rooted in the belief that we didn't need to complicate the project further. While I considered the use of Tailwind CSS for its simplicity in styling, I decided against it. My concern was that it might make the template sections challenging to read, particularly given how Vue 3 renders templates.

In designing the project, I divided it into components that made the most logical sense to me. This included implementing a 'hide' component for concealing specific data, a 'search' function for filtering based on various parameters, a 'table' component for displaying the data, and a 'pagination' component that improved data navigation. These components integrated with 'table.vue' to bring the project together. I consciously chose not to further modularize the functions within 'table.vue' to maintain a single source of truth, avoiding the complexity of passing additional props through 'table' and then through the functions. However, modularizing the code could be a future improvement worth considering.

This project posed several challenges. As I hadn't extensively used Vue before, I had to invest considerable time in learning Vue 3 and comprehending the composition API. Additionally, the pagination component proved more challenging than expected. It required careful design to work with 0-based indexing while ensuring users would see the correct page numbers.

One particular decision I made was to reuse the 'MainTable' code for the search function instead of creating a separate table component. I achieved this by structuring the 'filteredDisplayData' into an object that could be easily consumed by 'MainTable.' This approach saved me from writing a substantial amount of redundant code and streamlined the process.

The most demanding aspect of the project was working with ranges in the filter function. I encountered numerous issues related to null and undefined values, necessitating extensive console debugging to identify and rectify the problems.


### Bonus Features

I aimed to make the search function as user-friendly as possible. Each parameter is clearly labeled with its respective name, and two buttons are provided for filtering based on the specified parameters and resetting to display the original data when done. Instead of presenting information in rows of 100, I opted to display it directly within the table. This decision was driven by the belief that users searching for specific information should not have to navigate through an excessive number of rows.

Looking ahead, one potential enhancement for the future could involve the incorporation of Natural Language Processing. With NLP capabilities, users could issue more natural language queries like, 'Please find me a product that was launched and is named Core i3-1110G4...' This would greatly enhance the functionality and make the search feature even more powerful!

### Creativity and Initiative

In my opinion, the most crucial aspect of data visualization tools is their functionality. While the original tooling worked well, there were some ambiguities in how certain functions operated. To address this, I made several enhancements. Firstly, I opted for more explicit function names, such as 'Hide Function' and 'Search Function,' to clarify their intended purposes. Additionally, I added a dedicated row for 'All statuses,' which signifies the ability to hide all statuses simultaneously. Furthermore, I introduced a subtle green theme to the site. This theme not only enhances visual appeal but also emphasizes the key buttons on the screen. Lastly, I included a page number textbox to indicate the current page within the dataset. Collectively, these improvements have significantly enhanced the usability of the application.





