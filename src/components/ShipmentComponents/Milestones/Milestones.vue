<template>
    <div class="milestone-shipments-wrapper">
        <v-stepper v-model="e13" vertical>
            <div class="milestone-content">
                <div class="milestone-items">
                    
                    <v-stepper-step 
                        class="custom-vue-stepper"
                        :class="has-data"
                        :step="1"
                        color="#0171A1"
                        complete>
                        
                        <i aria-hidden='true' :class="custom-padding-left"></i>

                        <span>
                            hello
                        </span>

                        <span style="color: #EB9B26 !important;">
                            hello1
                        </span>

                        <div :class="getStatus(item)" class="released-event">
                            <span>hello3</span>

                            <span class="status no-holds">
                                N/A
                            </span>

                            <span class="status no" v-if="!item.noHolds && item.data !== null && !item.allContainersReleased">
                                <img src="../../../assets/icons/released-no.svg" width="10px" height="10px" alt="" class="mr-1"> No
                            </span>

                            <span class="status yes" v-if="item.allContainersReleased && item.isCompleted">
                                <img src="../../../assets/icons/checkMark.png" alt="" width="12px" height="12px" class="mr-1"> Yes
                            </span>
                        </div>
                    </v-stepper-step>

                    <v-stepper-content 
                        :step="index + 1"
                        v-if="item.event === 'booking_created' || item.event === 'booking_confirmed' && item.date !== null">
                        <small>{{ convertDate(item.event_name, item.date) }}</small>
                    </v-stepper-content>                    

                    <v-stepper-content
                        :step="index + 1"
                        v-if="(typeof item.data !== 'undefined' && item.event !== 'released_at_terminal' && item.event !== 'eta_logs')">

                        <div v-for="(data, index) in item.data" 
                            :key="index">

                            <div class="milestone-container-wrapper">
                                <div class="milestone-container-num">
                                    <p>{{ data.container_num }}</p>
                                </div>

                                <div class="milestone-container-dates">
                                    <span>
                                        <small v-if="data.date !== null">{{ convertDate(item.event_name, data.date) }}</small>
                                        <small v-if="item.event == 'last_free_day' && data.date == null">N/A</small>
                                    </span>
                                    <small style="marginLeft: 5px" v-if="isContainerDateComplete(data.date) !== false">
                                        <img class="completed-icon" src="../../../assets/icons/checkMark.png" width="10px" height="10px" v-if="item.event !== 'last_free_day'" />
                                    </small>
                                </div>
                            </div>                                      

                            <!-- Only for transshipments -->
                            <!-- <div v-if="getEventName(item.event)" class="milestone-relationship-wrapper">
                                <div v-for="(otherData, index) in data.data" :key="index" class="milestone-relationship-item">
                                    <div class="milestone-container-wrapper transshipments-other-data my-1">
                                        <p>Location:</p>
                                        <small>{{ otherData.relationships.location.data.type }}</small>
                                    </div>

                                    <div class="milestone-container-wrapper transshipments-other-data mt-1">
                                        <p>Vessel:</p>
                                        <small>{{ otherData.relationships.vessel.data.type }}</small>
                                    </div>

                                    <div class="separator"></div>
                                </div>
                            </div> -->
                        </div>
                    </v-stepper-content>

                    <v-stepper-content 
                        v-if="item.event === 'released_at_terminal'" 
                        :step="index + 1">

                        <div v-for="(newItem, index) in item.data" 
                            :key="index" 
                            class="milestone-container-wrapper released"
                            v-show="!item.noHolds && !item.allContainersReleased">

                            <div class="milestone-container-num">
                                <p>{{ newItem.container_num }}</p>
                            </div>

                            <div class="milestone-container-status" 
                                v-if="(typeof newItem.data !== 'undefined' && newItem.data !== null &&
                                    newItem.data.length !== 'undefined' && newItem.data.length > 0)">

                                <div class="status" v-for="(status, index) in newItem.data" :key="index">
                                    <!-- <span>{{ `${status.name} ${status.status}` }}</span> -->
                                    <span>{{ `${status.name} Pending` }}</span>
                                </div>
                            </div>

                            <!-- If no holds -->
                            <div class="milestone-container-status"
                            v-if="(typeof newItem.data !== 'undefined' && newItem.data !== null &&                              
                            newItem.data.length !== 'undefined' && newItem.data.length == 0)">

                                <span class="status yes" v-if="newItem.releasedEvent">
                                    <img src="../../../assets/icons/checkMark.png" alt="" width="12px" height="12px" class="mr-1"> Yes
                                </span>

                                <span class="status no" v-if="!newItem.releasedEvent">
                                    <img src="../../../assets/icons/released-no.svg" alt="" width="12px" height="12px" class="mr-1"> No
                                </span>
                            </div>                            
                        </div>
                    </v-stepper-content>

                    <v-stepper-content v-if="item.event === 'eta_logs'" :step="index + 1">
                        <div class="milestone-container-wrapper released">
                            <div class="milestone-container-num">
                                <p>The ETA of the Shipment has been updated from 
                                    {{ etaDateConvert(item.log.old_eta) }} to 
                                    {{ etaDateConvert(item.log.eta) }} </p>
                            </div>
                            <div>
                                <small>{{ convertDate(item.event_name, item.log.created_at) }}</small>
                            </div>
                        </div>
                    </v-stepper-content>

                    <v-stepper-content v-if="(item.data == null)" :step="index + 1">
                        <span> </span>
                    </v-stepper-content>
                </div>
            </div>
        </v-stepper>
    </div>
</template>

<script>

export default {
    name: "Milestones",
    data: () => ({
        e13: 1,
        containersLists: [],
        counter: 0
    }),
}
</script>

<style>
@import "../../../assets/css/shipments_styles/milestones.css";
</style>
