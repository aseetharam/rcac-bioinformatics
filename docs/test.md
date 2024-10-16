# test tab


testing the tabs interface with the following code:


<div>
  <button class="tablink" onclick="openTab(event, 'Tab1')">Tab 1</button>
  <button class="tablink" onclick="openTab(event, 'Tab2')">Tab 2</button>
  <button class="tablink" onclick="openTab(event, 'Tab3')">Tab 3</button>
</div>

<div id="Tab1" class="tabcontent" style="display:none">
  <h3>Tab 1</h3>
  <table>
    <tr>
      <th>Header 1</th>
      <th>Header 2</th>
    </tr>
    <tr>
      <td>Data 1</td>
      <td>Data 2</td>
    </tr>
  </table>
</div>

<div id="Tab2" class="tabcontent" style="display:none">
  <h3>Tab 2</h3>
  <table>
    <tr>
      <th>Header 3</th>
      <th>Header 4</th>
    </tr>
    <tr>
      <td>Data 3</td>
      <td>Data 4</td>
    </tr>
  </table>
</div>

<div id="Tab3" class="tabcontent" style="display:none">
  <h3>Tab 3</h3>
  <table>
    <tr>
      <th>Header 5</th>
      <th>Header 6</th>
    </tr>
    <tr>
      <td>Data 5</td>
      <td>Data 6</td>
    </tr>
  </table>
</div>

<script>
function openTab(evt, tabName) {
  var i, tabcontent, tablinks;
  tabcontent = document.getElementsByClassName("tabcontent");
  for (i = 0; i < tabcontent.length; i++) {
    tabcontent[i].style.display = "none";  
  }
  tablinks = document.getElementsByClassName("tablink");
  for (i = 0; i < tablinks.length; i++) {
    tablinks[i].className = tablinks[i].className.replace(" active", "");
  }
  document.getElementById(tabName).style.display = "block";  
  evt.currentTarget.className += " active";
}
</script>
