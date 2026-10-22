# Day-5


<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>User Directory</title>
</head>
<body>
  <h1>User Directory</h1>

  <button id="load-users">Load Users</button>

  <label for="filter-input">Filter users:</label>
  <input id="filter-input" type="text" placeholder="Search name, email or city">

  <p id="status" aria-live="polite"></p>

  <ul id="users-list"></ul>

  <script src="users.js"></script>
</body>
</html>




const loadButton = document.getElementById("load-users");
const filterInput = document.getElementById("filter-input");
const status = document.getElementById("status");
const usersList = document.getElementById("users-list");

let users = [];

async function loadUsers() {
  loadButton.disabled = true;
  status.textContent = "Loading users...";

  try {
    const response = await fetch(
      "https://jsonplaceholder.typicode.com/users"
    );

    if (!response.ok) {
      throw new Error(`HTTP error: ${response.status}`);
    }

    users = await response.json();
    renderUsers(users);

    status.textContent = `Loaded ${users.length} users.`;
  } catch (error) {
    status.textContent = `Error loading users: ${error.message}`;
    usersList.replaceChildren();
  } finally {
    loadButton.disabled = false;
  }
}

function renderUsers(usersToRender) {
  usersList.replaceChildren();

  if (usersToRender.length === 0) {
    status.textContent = "No users match your filter.";
    return;
  }

  usersToRender.forEach((user) => {
    const item = document.createElement("li");
    const name = document.createElement("h3");
    const email = document.createElement("p");
    const city = document.createElement("p");
    const company = document.createElement("p");

    name.textContent = user.name;
    email.textContent = `Email: ${user.email}`;
    city.textContent = `City: ${user.address.city}`;
    company.textContent = `Company: ${user.company.name}`;

    item.append(name, email, city, company);
    usersList.appendChild(item);
  });
}

loadButton.addEventListener("click", loadUsers);

filterInput.addEventListener("input", () => {
  const query = filterInput.value.trim().toLowerCase();

  const filteredUsers = users.filter((user) => {
    return [
      user.name,
      user.email,
      user.address.city,
      user.company.name
    ].some((value) => value.toLowerCase().includes(query));
  });

  renderUsers(filteredUsers);
});

