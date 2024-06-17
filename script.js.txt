document.addEventListener('DOMContentLoaded', function() {
  var password = "twojehaslo"; // Zmień na wybrane przez Ciebie hasło

  document.getElementById('loginForm').addEventListener('submit', function(event) {
    event.preventDefault();
    var enteredPassword = document.getElementById('password').value;
    if (enteredPassword === password) {
      document.getElementById('loginContainer').style.display = 'none';
      document.getElementById('content').style.display = 'block';
    } else {
      alert('Nieprawidłowe hasło!');
    }
  });

  var calendarEl = document.getElementById('calendar');
  var calendar = new FullCalendar.Calendar(calendarEl, {
    initialView: 'dayGridMonth',
    locale: 'pl',
    dateClick: function(info) {
      document.getElementById('selectedDate').value = info.dateStr;
      document.getElementById('passengerListDate').innerText = info.dateStr;
      document.getElementById('formSection').style.display = 'block';
      document.getElementById('passengerListSection').style.display = 'block';
      loadPassengers(info.dateStr);
    }
  });
  calendar.render();

  document.getElementById('passengerForm').addEventListener('submit', function(event) {
    event.preventDefault();
    var date = document.getElementById('selectedDate').value;
    var route = document.getElementById('route').value;
    var firstName = document.getElementById('firstName').value;
    var lastName = document.getElementById('lastName').value;
    var phoneNumber = document.getElementById('phoneNumber').value;
    var departure = document.getElementById('departure').value;
    var destination = document.getElementById('destination').value;
    var price = document.getElementById('price').value;
    var notes = document.getElementById('notes').value;

    var passenger = {
      route: route,
      firstName: firstName,
      lastName: lastName,
      phoneNumber: phoneNumber,
      departure: departure,
      destination: destination,
      price: price,
      notes: notes
    };

    savePassenger(date, passenger);
    loadPassengers(date);
    document.getElementById('passengerForm').reset();
  });
});

function savePassenger(date, passenger) {
  var passengers = JSON.parse(localStorage.getItem(date)) || [];
  passengers.push(passenger);
  localStorage.setItem(date, JSON.stringify(passengers));
}

function loadPassengers(date) {
  var passengers = JSON.parse(localStorage.getItem(date)) || [];
  var plDeList = document.getElementById('pl-de-list');
  var dePlList = document.getElementById('de-pl-list');
  plDeList.innerHTML = '';
  dePlList.innerHTML = '';
  passengers.forEach(function(passenger, index) {
    var li = document.createElement('li');
    li.innerHTML = `
      ${passenger.firstName} ${passenger.lastName} - ${passenger.phoneNumber} - ${passenger.departure} do ${passenger.destination} - ${passenger.price} zł
      <button onclick="deletePassenger('${date}', ${index})">Usuń</button>
    `;
    if (passenger.route === 'Polska-Niemcy') {
      plDeList.appendChild(li);
    } else {
      dePlList.appendChild(li);
    }
  });
}

function deletePassenger(date, index) {
  var passengers = JSON.parse(localStorage.getItem(date)) || [];
  passengers.splice(index, 1);
  localStorage.setItem(date, JSON.stringify(passengers));
  loadPassengers(date);
}
