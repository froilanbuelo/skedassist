<template>
    <div class="container">
        <div class="row">
            <div class="col-md-8 col-md-offset-2">
                
                <div class="panel panel-default">
                    <div class="panel-heading"><h3>Set Up a New Service</h3></div>

                    <div class="panel-body">
                    <h4>General Information</h4>
                        <div class="row">
                            <div class="col-md-9">
                                <div class="form-group">
                                    <label for="name">Name</label>
                                    <input type="text" name="name" id="name" class="form-control" v-model="service.name">
                                </div>
                                <div class="form-group">
                                    <label for="name">Description</label>
                                    <textarea class="form-control" rows="3" v-model="service.description"></textarea>
                                </div>
                            </div>
                            <div class="col-md-3">
                                <div class="form-group">
                                    <label for="image" class="hidden-sm hidden-xs">Image (optional)</label>
                                    <a href=""><img src="http://placehold.it/150x150" alt="..." class="img-rounded"></a>
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="col-md-12"><h4>Duration</h4></div>
                        </div>
                        <div class="row">
                            <div class="col-xs-2">
                                <div class="form-group">
                                    <label for="name">Hours</label>
                                    <input type="text" name="name" id="name" class="form-control" placeholder="HH" v-model="service.hours">
                                </div>
                            </div>
                            <div class="col-xs-2">
                                <div class="form-group">
                                    <label for="name">Minutes</label>
                                    <input type="text" name="name" id="name" class="form-control" placeholder="MM" v-model="service.minutes">
                                </div>
                            </div>
                            <div class="col-xs-3">
                                <div class="checkbox">
                                <label>
                                    <input type="checkbox" v-on:click="toggleAddBuffer"> Add Buffer Time
                                </label>
                                </div>
                            </div>
                            <div class="col-xs-2" v-if="add_buffer">
                                <div class="form-group">
                                    <label for="name">Before</label>
                                    <input type="text" name="name" id="name" class="form-control" placeholder="MM" v-model="service.buffer_before">
                                </div>
                            </div>
                            <div class="col-xs-2" v-if="add_buffer">
                                <div class="form-group">
                                    <label for="name">After</label>
                                    <input type="text" name="name" id="name" class="form-control" placeholder="MM" v-model="service.buffer_after">
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="col-md-12">
                                <a role="button" data-toggle="collapse" href="#collapseExample" aria-expanded="false" aria-controls="collapseExample">
                                  <h4>Schedule <span class="glyphicon glyphicon-menu-down" aria-hidden="true"></span></h4>
                                </a>
                                <div class="collapse" id="collapseExample">
                                    <span v-if="service.schedules.length <= 0">No Schedule set.</span>
                                    <ul id="example-1">
                                        <li v-for="(schedule, keySchedule) in service.schedules">
                                        <h4>{{ schedule.start_time }} - {{ schedule.end_time }} - {{ schedule.rolling_days }} {{ schedule.type }} 
                                        </h4> 
                                        <h4 v-if="schedule.start_date !== null">
                                        (From {{ new Date(schedule.start_date).toLocaleDateString("en-US",dateOptions) }} To {{ new Date(schedule.end_date).toLocaleDateString("en-US",dateOptions) }} )</h4>
                                        <span v-for="(value, key) in schedule.days">
                                        {{ value }},
                                        </span>
                                        <span class="glyphicon glyphicon-trash text-danger" aria-hidden="true" v-on:click="deleteSchedule(keySchedule)"></span>
                                        </li>
                                    </ul>   
                                     
                                    <div class="well">
                                    <a href="" v-if="!showScheduleForm" v-on:click.prevent="showAddScheduleForm">Add Schedule</a>
                                    <div class="row" v-if="showScheduleForm">
                                        <div class="col-md-3">
                                            <div class="form-group">
                                                <label for="name">Type</label>
                                                <select class="form-control" v-model="scheduleForm.type">
                                                    <option value='Infinite Days'>Infinite Days</option>
                                                    <option value='Date Range'>Date Range</option>
                                                    <option value='Rolling Days'>Rolling Days</option>
                                                </select>
                                            </div>
                                        </div>
                                        <div class="col-md-2" v-if="scheduleForm.type=='Rolling Days'">
                                            <div class="form-group">
                                                <label for="name">Days</label>
                                                <input type="text" name="" class="form-control" v-model="scheduleForm.rolling_days">
                                            </div>
                                        </div>
                                        <div class="col-md-2" v-if="scheduleForm.type=='Date Range'">
                                            <div class="form-group">
                                                <label for="name">Start Date</label>
                                                <datepicker :format="dateFormat" :input-class="formControl" v-model="scheduleForm.start_date" name="range_start_date"></datepicker>
                                            </div>
                                        </div>
                                        <div class="col-md-2" v-if="scheduleForm.type=='Date Range'">
                                            <div class="form-group">
                                                <label for="name">End Date</label>
                                                <datepicker :format="dateFormat" :input-class="formControl" v-model="scheduleForm.end_date" name="range_end_date"></datepicker>
                                            </div>
                                        </div>
                                        <div class="col-md-2">
                                            <div class="form-group">
                                                <label for="name">Start Time</label>
                                                <input type="text" name="" class="form-control" v-model="scheduleForm.start_time">
                                            </div>
                                        </div>
                                        <div class="col-md-2">
                                            <div class="form-group">
                                                <label for="name">End Time</label>
                                                <input type="text" name="" class="form-control" v-model="scheduleForm.end_time">
                                            </div>
                                        </div>
                                    </div>
                                    <div class="row" v-if="showScheduleForm">
                                        <div class="col-md-12">
                                            <input type="checkbox" id="Sunday" value="Sunday" v-model="scheduleForm.days">
                                            <label for="Sunday">Sunday</label>
                                             <input type="checkbox" id="Monday" value="Monday" v-model="scheduleForm.days">
                                            <label for="Monday">Monday</label>
                                            <input type="checkbox" id="Tuesday" value="Tuesday" v-model="scheduleForm.days">
                                            <label for="Tuesday">Tuesday</label>
                                            <input type="checkbox" id="Wednesday" value="Wednesday" v-model="scheduleForm.days">
                                            <label for="Wednesday">Wednesday</label>
                                            <input type="checkbox" id="Thursday" value="Thursday" v-model="scheduleForm.days">
                                            <label for="Thursday">Thursday</label>
                                            <input type="checkbox" id="Friday" value="Friday" v-model="scheduleForm.days">
                                            <label for="Friday">Friday</label>
                                            <input type="checkbox" id="Saturday" value="Saturday" v-model="scheduleForm.days">
                                            <label for="Saturday">Saturday</label>
                                        </div>
                                    </div>
                                    <div class="row" v-if="showScheduleForm">
                                        <div class="col-md-2">
                                            <input type="button" name="" class="form-control" value="Add" v-on:click="addSchedule">
                                        </div>
                                        <div class="col-md-2">
                                            <input type="button" name="" class="form-control" value="Close" v-on:click="hideAddScheduleForm">
                                        </div>
                                    </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="col-md-12">
                                <button class="pull-right btn btn-primary">Save Changes</button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import Datepicker from 'vuejs-datepicker';

    export default {
        data() {
            return {
                add_buffer: false,
                dateOptions: { weekday: 'short', year: 'numeric', month: 'long', day: 'numeric' },
                formControl: 'form-control',
                dateFormat: 'yyyy-MM-dd',
                showScheduleForm: false,
                scheduleForm: {
                    type: 'Infinite Days',
                    rolling_days: '',
                    start_date: null,
                    end_date: null,
                    start_time: '8am',
                    end_time: '5pm',
                    all_days: false,
                    days: [
                        'Monday',
                        'Tuesday',
                        'Wednesday',
                        'Thursday',
                        'Friday',
                    ]
                },
                service: {
                    name: '',
                    description: '',
                    hours: '',
                    minutes: '15',
                    buffer_before: '',
                    buffer_after: '',
                    schedules: [
                        {
                            type: 'Infinite Days',
                            rolling_days: '',
                            start_date: null,
                            end_date: null,
                            start_time: '8am',
                            end_time: '12nn',
                            all_days: false,
                            days: [
                                'Monday',
                                'Tuesday',
                                'Wednesday',
                                'Thursday',
                                'Friday',
                            ]
                        },
                        {
                            type: 'Infinite Days',
                            rolling_days: '',
                            start_date: null,
                            end_date: null,
                            start_time: '1pm',
                            end_time: '5pm',
                            all_days: false,
                            days: [
                                'Monday',
                                'Tuesday',
                                'Wednesday',
                                'Thursday',
                                'Friday',
                            ]
                        }
                    ]
                }
            }
        },
        mounted() {
            console.log('Component mounted.')
        },
        methods: {
            toggleAddBuffer: function(event){
                this.add_buffer = ! this.add_buffer
                if (!this.add_buffer){
                    this.service.buffer_before = ''
                    this.service.buffer_after = ''
                }else{
                    this.service.buffer_before = '15'
                    this.service.buffer_after = '15'
                }
            },
            deleteSchedule: function (key){
                Vue.delete(this.service.schedules, key);
            },
            showAddScheduleForm: function(){
                this.showScheduleForm = true
            },
            hideAddScheduleForm: function(){
                this.showScheduleForm = false
            },
            addSchedule: function(){
                // this.scheduleForm.start_date = new Date(this.scheduleForm.start_date)
                // this.scheduleForm.end_date = new Date(this.scheduleForm.end_date)
                if (this.scheduleForm.type == 'Infinite Days' || this.scheduleForm.type == 'Rolling Days'){
                    this.scheduleForm.start_date = null
                    this.scheduleForm.end_date = null
                }else if (this.scheduleForm.type == 'Date Range'){
                    this.scheduleForm.rolling_days = ''
                }
                this.service.schedules.push(JSON.parse(JSON.stringify(this.scheduleForm)));
            }
        },
        components: {
            Datepicker
        }
    }
</script>
