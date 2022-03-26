<template>
  <div id="container">
    <div id="time-start">
      <table class="header-table">
        <tr>
          <th>开始时间</th>
        </tr>
        <tr>
          <td>
            <!-- <input v-model="startTime" name="time-start" type="date-time"> -->
            <span>{{startTimeFormat}}</span>
          </td>
        </tr>
      </table>
    </div>
    <div id="time-interval">
      <table  class="header-table">
        <tr>
          <th>时间段</th>
        </tr>
        <tr>
          <td>
            <!-- <input v-model="timeInterval" name="time-interval" type="number"> -->
            <span>30 分钟</span>
          </td>
        </tr>
      </table>
    </div>
    <div id="title"><span class="title-text">寄摆日程</span></div>
    <div id="date"><span class="date-text">{{date.getFullYear()}}年{{date.getMonth()}}月{{date.getDate()}}日</span></div>
    <div id="content" class="component">
      <table role="table" :data="scheduleList" class="datatable">
        <thead>
          <tr>
            <th>时间</th>
            <th>安排</th>
            <th>操作</th>
          </tr>
        </thead>
        <tbody v-for="schedule of scheduleList" :key="schedule.label">
          <tr>
            <td><span>{{schedule.label}}</span></td>
            <td><span>{{schedule.value}}</span></td>
            <td>
              <button class="btn btn-icon" @click="edit(schedule)">
                <svg t="1648307635933" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="3212" width="20" height="20"><path d="M864.711111 267.377778l-164.977778-164.977778 85.333333-85.333333c28.444444-28.444444 73.955556-28.444444 96.711111 0l68.266667 68.266667c28.444444 28.444444 28.444444 73.955556 0 96.711111L864.711111 267.377778zM386.844444 745.244444 221.866667 580.266667l449.422222-443.733333 164.977778 164.977778L386.844444 745.244444zM91.022222 881.777778l102.4-267.377778 164.977778 164.977778L91.022222 881.777778zM830.577778 938.666667c22.755556 0 39.822222 17.066667 39.822222 39.822222 0 22.755556-17.066667 39.822222-39.822222 39.822222l-739.555556 0C68.266667 1024 51.2 1001.244444 51.2 978.488889c0-22.755556 17.066667-39.822222 39.822222-39.822222L830.577778 938.666667z" p-id="3213" fill="#8fbc8f"></path></svg>
              </button>
              <button class="btn btn-icon" @click="clear(schedule)">
                <svg t="1648307672406" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="4048" width="20" height="20"><path d="M341.312 85.312l64-85.312h213.376l64 85.312H960v85.376H64V85.312h277.312zM170.688 256h682.624v768H170.688V256zM256 341.312v597.376h512V341.312H256z m213.312 85.376v426.624H384V426.688h85.312z m170.688 0v426.624H554.688V426.688H640z" fill="#8fbc8f" p-id="4049"></path></svg>
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
      <div v-if="isEditable" class="dialog">
  <div class="dialog-mask">
      <div class="dialog-header">
        <span style="float:left">编辑{{selectSchedule.label}}</span>
        <div class="dialog-header-btns">
          <button @click="cancle" class="btn btn-cancle-icon btn-dialog-header-icon">
            <svg t="1648302924321" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="2369" width="20" height="20"><path d="M176.661601 817.172881C168.472798 825.644055 168.701706 839.149636 177.172881 847.338438 185.644056 855.527241 199.149636 855.298332 207.338438 846.827157L826.005105 206.827157C834.193907 198.355983 833.964998 184.850403 825.493824 176.661601 817.02265 168.472798 803.517069 168.701706 795.328267 177.172881L176.661601 817.172881Z" p-id="2370" fill="#8fbc8f"></path><path d="M795.328267 846.827157C803.517069 855.298332 817.02265 855.527241 825.493824 847.338438 833.964998 839.149636 834.193907 825.644055 826.005105 817.172881L207.338438 177.172881C199.149636 168.701706 185.644056 168.472798 177.172881 176.661601 168.701706 184.850403 168.472798 198.355983 176.661601 206.827157L795.328267 846.827157Z" p-id="2371" fill="#8fbc8f"></path></svg>
          </button>
        </div>
      </div>
      <div class="dialog-body">
        <input type="text" 
               v-model="editValue"
               class="input"
               placeholder="寄寄寄摆摆摆摆">
      </div>
      <div class="dialog-footer">
        <button @click="confirm" class="btn btn-check">
          <div class="btn-icon">
            <svg t="1648318102155" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="2571" width="20" height="20"><path d="M874.322026 254.904551l-389.17358 506.198877c-3.530406 4.594645-8.861832 7.480368-14.643512 7.930623-0.532119 0.040932-1.054005 0.061398-1.586125 0.061398-5.229095 0-10.284227-2.00568-14.101159-5.64865l-210.013131-199.8824-32.397874 42.149982c-6.897084 8.953929-19.739577 10.642384-28.703739 3.745301-8.964162-6.886851-10.642384-19.739577-3.745301-28.693506l46.263673-60.180638c3.540639-4.594645 8.861832-7.480368 14.643512-7.930623s11.491729 1.586125 15.687284 5.587252l210.013131 199.8824 375.297548-488.168222c6.897084-8.964162 19.739577-10.642384 28.703739-3.755534C879.530654 233.097896 881.208876 245.940388 874.322026 254.904551z" p-id="2572" fill="#8fbc8f"></path></svg>
          </div>
        
          <span class="btn-text">OK</span>
        </button>
      </div>
    </div>
  </div>
  </div>
  
</template>

<script>
export default {
  name: 'Schedule',
  data() {
    return {
      startTime: new Date(),
      timeInterval: 30,
      date: new Date(),
      scheduleList: [],
      isEditable: false,
      selectSchedule: {},
      editValue: '',
    }

  },
  created() {
    let endTime = new Date(this.startTime.getTime() + 24 * 60 * 60 * 1000);
    endTime.setHours(2);
    endTime.setMinutes(0);
    endTime.setSeconds(0);
    if (this.startTime.getMinutes() < 30) {
      this.startTime = new Date(this.startTime.getTime() + (30 - this.startTime.getMinutes()) * 60 * 1000)
    } else if (this.startTime.getMinutes() > 30) {
      this.startTime = new Date(this.startTime.getTime() + (60 - this.startTime.getMinutes()) * 60 * 1000)
    }
    let startTimeTemp = new Date(this.startTime.getTime());
    while(startTimeTemp < endTime) {
      let section = new Date(startTimeTemp.getTime() + 30 * 60 * 1000);
      this.scheduleList.push({
        label: `${startTimeTemp.getHours().toString().padStart(2, '0')}: ${
          startTimeTemp.getMinutes().toString().padStart(2, '0')}~~ ${
            (section.getHours()).toString().padStart(2, '0')}: ${
              section.getMinutes().toString().padStart(2, '0')
        }`,
        value: ''
      });
      startTimeTemp = new Date(section.getTime());
    }
  },
  computed: {
    startTimeFormat() {
      return `${this.startTime.getHours().toString().padStart(2, '0')}: ${this.startTime.getMinutes().toString().padStart(2, '0')}`;
    }
  },
  methods: {
    edit(event) {
      this.isEditable = true;
      this.selectSchedule = event;
      this.editValue = this.selectSchedule.value;
    },
    confirm() {
      this.selectSchedule.value = this.editValue;
      this.isEditable = false;
    },
    cancle() {
      this.isEditable = false;
      this.editValue = '';
    },
    clear(event) {
      event.value = '';
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.component {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
  font-size: 1rem;
  font-weight: normal;
}
#container {
  display: grid;
  grid-gap: 1vw;
  grid-template-rows: 15vh;
  grid-auto-rows: 13vh;
  grid-template-columns: 1fr 1fr 3fr;
}

#date {
  grid-column-end: span 3;
}
#date .date-text {
  float: right;
  font-size: 1.5rem;
  font-weight: 500;
  line-height: 8vh;
}

#title {
  background-color: darkseagreen;
  font-size: 3rem;
  font-weight: 700;
  color: white;
  margin-bottom: 1vh;
}

#title .title-text {
  float: right;
  line-height: 15vh;
  
}

.header-table {
  font: var(--type-body-l);
  border: 1px solid #8f8b8b;
  border-collapse: collapse;
}
.header-table td, .header-table th {
    padding: 0.5rem 0.75rem;
    border: 1px solid #e5e5e5;
    vertical-align: middle;
    min-width: 15vw;
}
.header-table th {
    text-align: center;
    background: #e7e5e5;
    font-weight: 500;
    line-height: 1.5;
}
#content {
  grid-column-end: span 3;
  grid-row-end: span 10;
}

.dialog-mask  {
  display: flex;
  flex-direction: column;
  pointer-events: auto;
  max-height: 90%;
  transform: scale(1);
  position: relative;
  border-radius: 6px;
  box-shadow: 0 1px 3px rgb(0 0 0 / 30%);
  border: 0 none;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
  font-size: 1rem;
  font-weight: normal;
  background: #ffffff;
}

.dialog {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

.dialog-header {
  border-bottom: 0 none;
  color: #495057;
  padding: 1.5rem;
  border-top-right-radius: 6px;
  border-top-left-radius: 6px;
  font-weight: 400;
  font-size: 1.25rem;
}

.dialog-body {
  color: #495057;
  padding: 0 1.5rem 2rem 1.5rem;
  overflow-y: auto;
  flex-grow: 1;
}

.dialog-footer {
  border-top: 0 none;
  color: #495057;
  padding: 0 0.5rem 0.5rem 1.5rem;
  text-align: right;
  border-bottom-right-radius: 6px;
  border-bottom-left-radius: 6px;
}

.btn {
  margin: 0;
  display: inline-flex;
  cursor: pointer;
  -webkit-user-select: none;
  user-select: none;
  align-items: center;
  vertical-align: bottom;
  text-align: center;
  overflow: hidden;
  position: relative;
  color: #ffffff;
  background: transparent;
  border: 1px solid transparent;
  padding: 0.25rem 0.25rem;
  font-size: 1rem;
  transition: background-color 0.2s, color 0.2s, border-color 0.2s, box-shadow 0.2s;
  border-radius: 6px;
}

.btn .btn-text {
 background-color: transparent;
  color: darkseagreen;
  border-color: transparent;
  font-weight: 600;
}
.btn-icon {
  margin-right: 0.25rem;
  display: inline-block;
  font-style: normal;
  font-weight: 400;
  line-height: 0;
  color: darkseagreen
}

.btn-check:hover {
  background-color: rgb(215, 230, 215);
  border-radius: 6px;
}
.btn-cancle-icon {
  color: darkseagreen;
  width: 2rem;
  height: 2rem;
  border: 0 none;
  background: transparent;
  border-radius: 50%;
  transition: background-color 0.2s, color 0.2s, box-shadow 0.2s;
  margin-right: 0.5rem;
  font-size: 1rem;
}

.btn-dialog-header-icon {
  display: flex;
  justify-content: center;
  align-items: center;
}

.btn-cancle-icon:hover {
  background-color: rgb(215, 230, 215);
}

.dialog-header-btns {
  display: inline-block;
  float: right;
}

.input {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
  font-size: 1rem;
  color: #495057;
  padding: 0.75rem 0.75rem;
  border: 1px solid #ced4da;
  transition: background-color 0.2s, color 0.2s, border-color 0.2s, box-shadow 0.2s;
  border-radius: 6px;
  min-width: 17rem
}

.input:focus {
  outline: 0 none;
  outline-offset: 0;
  box-shadow: 0 0 0 0.05rem rgb(225, 233, 225);
  border-color: rgb(215, 230, 215);
}

.datatable {
  border-collapse: collapse;
  min-width: 100%;
  table-layout: auto;
}

.datatable>thead>tr>th {
  text-align: center;
  padding: 1rem 1rem;
  border: 1px solid #dee2e6;
  border-width: 0 0 1px 0;
  font-weight: 700;
  color: #343a40;
  background: #f8f9fa;
  transition: box-shadow 0.2s;
}

.datatable>tbody>tr>td {
  background: #fff;
  color: #495057;
  transition: box-shadow 0.2s;
  padding: 1rem 1rem;
  border: 1px solid #dee2e6;
  border-width: 0 0 1px 0;
}

.datatable>tbody>tr:hover {
  background: #e9ecef;
  color: #495057;
}

.datatable>tbody>tr:hover td {
  background: none;
}

td {
  text-align: center;
}
</style>
