---
layout: page
title: Reading
permalink: /reading/
---

<div class="leaf">
  <h1 class="chapter-title sc">Reading</h1>
  <p class="dek">what&rsquo;s been read, and what&rsquo;s still on the shelf</p>

  <div class="prose dropcap-host" id="reading-intro">
    <p>Titles drift on and off this shelf as I make my way through them. A small green tick in the margin marks the ones I&rsquo;ve finished; a note in gold marks the ones still waiting.</p>
  </div>

  <ol class="book-index" id="book-index" aria-label="Books"></ol>
</div>

<script>
document.addEventListener('DOMContentLoaded', function () {
  fetch('{{ site.baseurl }}/data/books.json')
    .then(function (response) {
      if (!response.ok) throw new Error('Network response was not ok: ' + response.statusText);
      return response.json();
    })
    .then(function (data) {
      var books = data.books || [];
      var read = books.filter(function (b) { return b.status !== 'on-the-shelf'; });
      var shelf = books.filter(function (b) { return b.status === 'on-the-shelf'; });
      var list = document.getElementById('book-index');
      var showHeadings = read.length > 0 && shelf.length > 0;

      function renderEntry(book, isRead) {
        var li = document.createElement('li');
        li.className = 'book-entry';

        var heading = document.createElement('p');
        heading.className = 'book-heading';
        var title = document.createElement('span');
        title.className = 'book-title';
        title.textContent = book.title;
        var author = document.createElement('span');
        author.className = 'book-author';
        author.textContent = book.author;
        heading.appendChild(title);
        heading.appendChild(author);

        var desc = document.createElement('p');
        desc.className = 'book-desc';
        desc.textContent = book.description;

        var aside = document.createElement('aside');
        aside.className = 'margin-note';
        var rule = document.createElement('span');
        rule.className = 'rule';
        rule.setAttribute('aria-hidden', 'true');
        aside.appendChild(rule);

        var mark = document.createElement('span');
        if (isRead) {
          mark.className = 'mark mark--read';
          mark.innerHTML = '<svg viewBox="0 0 16 16" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M2,9.2 C4,10.9 5.6,12.6 7,14.3 C10,10.2 12.8,6 15,2.4"/></svg><span>finished</span>';
          aside.appendChild(mark);
          var vh = document.createElement('span');
          vh.className = 'visually-hidden';
          vh.textContent = 'Read.';
          aside.appendChild(vh);
        } else {
          mark.className = 'mark mark--unread';
          mark.textContent = 'on the shelf';
          aside.appendChild(mark);
          var vh2 = document.createElement('span');
          vh2.className = 'visually-hidden';
          vh2.textContent = 'On the shelf, unread.';
          aside.appendChild(vh2);
        }

        li.appendChild(heading);
        li.appendChild(desc);
        li.appendChild(aside);
        return li;
      }

      if (showHeadings) {
        var readHeading = document.createElement('p');
        readHeading.className = 'shelf-heading sc';
        readHeading.textContent = 'Read';
        list.appendChild(readHeading);
      }
      read.forEach(function (book) { list.appendChild(renderEntry(book, true)); });

      if (showHeadings) {
        var shelfHeading = document.createElement('p');
        shelfHeading.className = 'shelf-heading sc';
        shelfHeading.textContent = 'On the Shelf';
        list.appendChild(shelfHeading);
      }
      shelf.forEach(function (book) { list.appendChild(renderEntry(book, false)); });
    })
    .catch(function (error) {
      console.error('Error loading books:', error);
      var list = document.getElementById('book-index');
      list.innerHTML = '<li class="empty-note">Could not load the reading list. Please try again later.</li>';
    });
});
</script>
