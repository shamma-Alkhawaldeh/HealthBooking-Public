<template>
  <div>
    <nav class="navbar navbar-light bg-white shadow-sm mb-4">
      <div class="container d-flex justify-content-between align-items-center">
        <span class="fw-bold fs-5">Doctor Appointments</span>
        <router-link to="/" class="btn btn-outline-primary">⇆ Switch Page</router-link>
      </div>
    </nav>

    <div class="d-flex justify-content-center align-items-start" style="min-height: 80vh;">
      <div class="card shadow" style="width: 100%; max-width: 850px;">
        <h2 class="p-3 m-0">Appointments</h2>

        <table class="table table-bordered m-0">
          <thead>
            <tr>
              <th>Name</th>
              <th>Symptoms</th>
              <th>Time</th>
              <th>Status</th>
              <th>Update</th>
            </tr>
          </thead>

          <tbody>
            <tr v-if="appointments.length === 0">
              <td colspan="5" class="text-center">No appointments found</td>
            </tr>

            <tr v-for="appointment in appointments" :key="appointment.appointmentId">
              <td>{{ appointment.patientName || appointment.name }}</td>
              <td>{{ appointment.symptoms }}</td>
              <td>{{ appointment.slot || appointment.time || appointment.createdAt }}</td>
              <td>{{ appointment.status }}</td>
              <td>
                <button
                  class="btn btn-success btn-sm"
                  @click="updateStatus(appointment.appointmentId)"
                >
                  Complete
                </button>
              </td>
            </tr>
          </tbody>
        </table>

      </div>
    </div>
  </div>
</template>

<script>
const API_BASE_URL = "https://mg843bat2b.execute-api.us-east-1.amazonaws.com/prod";

export default {
  name: "AppointmentsList",

  data() {
    return {
      appointments: []
    };
  },

  mounted() {
    this.fetchAppointments();
  },

  methods: {
    fetchAppointments() {
      fetch(`${API_BASE_URL}/appointments`)
        .then(res => res.json())
        .then(data => {
          let parsed = [];

          if (Array.isArray(data)) {
            parsed = data;
          } else if (data && Array.isArray(data.appointments)) {
            parsed = data.appointments;
          } else if (data && data.body) {
            const body =
              typeof data.body === "string"
                ? JSON.parse(data.body)
                : data.body;

            if (Array.isArray(body)) {
              parsed = body;
            } else if (body && Array.isArray(body.appointments)) {
              parsed = body.appointments;
            }
          }

          this.appointments = parsed;
          console.log("Appointments loaded:", this.appointments);
        })
        .catch(err => {
          console.error("Error loading appointments:", err);
          this.appointments = [];
        });
    },

    updateStatus(appointmentId) {
      fetch(`${API_BASE_URL}/appointments/${appointmentId}`, {
        method: "PATCH",
        headers: {
          "Content-Type": "application/json"
        },
        body: JSON.stringify({
          status: "Completed"
        })
      })
        .then(res => res.json())
        .then(() => {
          alert("Appointment updated!");
          this.fetchAppointments();
        })
        .catch(err => {
          console.error("Error updating appointment:", err);
          alert("Failed to update appointment.");
        });
    }
  }
};
</script>
