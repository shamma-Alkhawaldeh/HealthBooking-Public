<script>
export default {
  name: "BookAppointment",
  data() {
    return {
      name: "",
      symptoms: "",
      selectedSlot: "",
      slots: []
    };
  },

  mounted() {
    fetch("https://mg843bat2b.execute-api.us-east-1.amazonaws.com/prod/slots")
      .then(res => res.json())
      .then(data => {
        const parsed = Array.isArray(data) ? data : JSON.parse(data.body);

        this.slots = parsed
          .filter(s => s.isBooked === false)
          .map(s => s.slot);
      })
      .catch(err => {
        console.error("Error loading slots:", err);
      });
  },

  methods: {
    submitAppointment() {
      const payload = {
        patientName: this.name,
        symptoms: this.symptoms,
        slot: this.selectedSlot
      };

      fetch("https://mg843bat2b.execute-api.us-east-1.amazonaws.com/prod/appointments", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(payload)
      })
        .then(res => res.json())
        .then(() => {
          alert("Appointment booked!");
          this.name = "";
          this.symptoms = "";
          this.selectedSlot = "";
        })
        .catch(err => {
          console.error("Error booking appointment:", err);
          alert("Failed to book appointment.");
        });
    }
  }
};
</script>
