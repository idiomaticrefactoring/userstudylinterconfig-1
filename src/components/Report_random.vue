<template>
  <div class="container1">
    <div class="question-header">
      <h2>{{ `Question ${questionIndex + 1}:` }}</h2>
      <div class="timer">{{ `Time: ${minutes}:${seconds}` }}</div>
    </div>
    <div class="container2">
      <p class="question-title" v-html="question.title"></p>
      <p class="question-note">Note: Find all code pairs consisting of non-idiomatic code and idiomatic code. <b style="color: red; font-size: 1rem;" >The 'References' section provides the corresponding refactored code for the non-idiomatic code for your reference.</b> <b style="font-size: 1rem;">Place the non-idiomatic code in the 'Original Code' section and the corresponding idiomatic code in the 'Refactored Code' section. There is one or more code pairs. Click the '+' button to add another code pair.</b> If, within 5 minutes, you believe that there is no code to refactor or you cannot identify code that can be refactored, can skip it.</p>
      <p>References:</p>
      <pre>
  <code class="question-references" v-html="question.references" style="display:block; white-space:pre-wrap">
  </code></pre>

      <div v-for="index in tupleIndex" >
        <ReportTuple :question="question" :able-edit="ableEdit" :key="index" :name="index" :ref="'reportTuples_'+index"/>
        <hr>
      </div>
      <button @click="addQuestion" :disabled="!ableEdit">+</button>
    </div>
    <div class="container3">
      <button @click="nextQuestion" class="next-button">Next</button>
    </div>
  </div>
</template>

<script>
import ReportTuple from './ReportTuple.vue'
import { saveAs } from 'file-saver';

export default {
  name: "Report",
  components: {
    ReportTuple
  },
  data() {
    return {
      questionIndex: 0,
      minutes: 10,
      seconds: 0,
      tupleIndex: 1,
      ableEdit: true,
      answers: [],
      finalStoredData:{},
      questions: [
 {
          title: 'Refactor the following Python code with list comprehension, e.g., a= [1 for i in range(2)]. <p><a href="https://github.com/microsoft/nni/blob/767ed7f22e1e588ce76cbbecb6c6a4a76a309805/nni/algorithms/hpo/networkmorphism_tuner/graph.py#L866-L908">Right Click the Link to See Code in A New Tab!</a></p>',
          references: "<b>code 1 (line 877):</b>type_pairs = [] \n \
for item in graph.operation_history: \n \
    if item[0] == 'to_deeper_model': \n \
        operation_history.append([item[0], item[1], layer_description_extractor(item[2], graph.node_to_id)]) \n \
    else: \n \
        operation_history.append(item) \n \
\n \
<b>refactored code 1:</b>operation_history = [[item[0], item[1], layer_description_extractor(item[2], graph.node_to_id)] if item[0] == 'to_deeper_model' else item for item in graph.operation_history] \n \
\n \
<b>code 2 (line 894):</b>for node in graph.node_list: \n \
    node_id = graph.node_to_id[node] \n \
    node_information = node.shape \n \
    node_list.append((node_id, node_information)) \n \
<b>refactored code 2:</b>node_list = [(graph.node_to_id[node], node.shape) for node in graph.node_list] \n \
\n \
<b>code 3 (line 899):</b>for (layer_id, item) in enumerate(graph.layer_list): \n \
    layer = graph.layer_list[layer_id] \n \
    layer_information = layer_description_extractor(layer, graph.node_to_id) \n \
    layer_list.append((layer_id, layer_information)) \n \
<b>refactored code 3:</b>layer_list = [(layer_id, layer_description_extractor(graph.layer_list[layer_id], graph.node_to_id)) for (layer_id, item) in enumerate(graph.layer_list)]"
        },
{
title: 'Refactor the following Python code with a For statement with multiple targets, e.g., <code>"for i in a: print(i[0]) can be refactored into for i_0, *i_remain in a: print(i_0)"</code>. <p><a href="https://github.com/franMarz/TexTools-Blender/blob/d2c324f9f4e986c2cdb00da6f0fd0999bb871203/utilities_uv.py#L137-L207">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code 1 (line 195):</b>for uv_set in settings.selection_uv_loops: \n \
    for loop in bm.faces[uv_set[0]].loops: \n \
        if loop.vert.index == uv_set[1]: \n \
            loop[uv_layers].select = True \n \
            break \n \
<b>refactored code1:</b>for (uv_set_0, uv_set_1, *uv_set_len) in settings.selection_uv_loops: \n \
    for loop in bm.faces[uv_set_0].loops: \n \
        if loop.vert.index == uv_set_1: \n \
            loop[uv_layers].select = True \n \
            break"
        },
{
title: 'Refactor the following Python code with chain comparison, e.g., <code>2 > a > 1</code>. <p><a href="https://github.com/JiaxuanYou/graph-generation/blob/3444b8ad2fd7ecb6ade45086b4c75f8e2e9f29d1/create_graphs.py#L7-L153">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code 1 (line 137):</b>G_ego.number_of_nodes() >= 50 and G_ego.number_of_nodes() <= 400 \n \
<b>refactored code1:</b>50 <= G_ego.number_of_nodes() <= 400 \n \
 \n \
<b>code 2:  (line 147)</b>G_ego.number_of_nodes() >= 4 and G_ego.number_of_nodes() <= 20 \n \
<b>refactored code2:4 <= G_ego.number_of_nodes() <= 20"
        },
{
title: 'Refactor the following Python code with While/For statement with else clause, e.g., <code>"for i in a:\n \ pass \n \ else: \n \ pass"</code>. <p><a href="https://github.com/forseti-security/forseti-security/blob/9069bfb04e818e51f484d860a249de0d578d4cf3/google/cloud/forseti/services/dao.py#L841-L872">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code 1 (line 861):</b>is_root = True \n \
for children in resource_hierarchy.values(): \n \
    if parent in children: \n \
        is_root = False \n \
        break \n \
if is_root: \n \
    root = parent \n \
<b>refactored code1:</b>for children in resource_hierarchy.values(): \n \
    if parent in children: \n \
        break \n \
else: \n \
    root = parent"
        },
{
title: 'Refactor the following Python code with set comprehension, e.g., a= {i for i in range(2)}. <p><a href="https://github.com/mozilla/pontoon/blob/3a95b7bb3c3982cad3320898ee9b791dfa1267ed/pontoon/insights/tasks.py#L386-L429">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code (line 400):</b>for user in privileged_users: \n \
    manager = user['managers_group__user'] \n \
    last_login = user['managers_group__user__last_login'] \n \
    if last_login: \n \
        if last_login + relativedelta(months=months) > start_of_today: \n \
            active_managers.add(manager) \n \
<b>refactored code:</b>active_managers = {user['managers_group__user'] for user in privileged_users if user['managers_group__user__last_login'] if user['managers_group__user__last_login'] + relativedelta(months=months) > start_of_today}"
        },
{
title: 'Refactor the following Python code with chain comparison, e.g., <code>2 > a > 1</code>. <p><a href="https://github.com/wbond/package_control/blob/ec6555920e41d195a1721c7a0bcb6fa68e4098aa/package_control/deps/oscrypto/trust_list.py#L65-L138">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code 1 (line 110):</b>trust_oids != empty_set and any_purpose not in trust_oids and (win_server_auth not in trust_oids) \n \
<b>refactored code1:</b>any_purpose not in trust_oids != empty_set and win_server_auth not in trust_oids \n \
 \n \
<b>code 2:  (line 121)</b>trust_oids != empty_set and any_purpose not in trust_oids and (apple_ssl not in trust_oids) \n \
<b>refactored code2:any_purpose not in trust_oids != empty_set and apple_ssl not in trust_oids"
        },
{
title: 'Refactor the following Python code with an Assignment statement using a value to assign multiple targets, e.g., <code>"a=1; b=1 can be refactored into a=b=1"</code>. <p><a href="https://github.com/IntelLabs/coach/blob/2c60cb5acd8cd3c9c381a5066c208e69fc273c7b/rl_coach/dashboard_components/signals_file_base.py#L26-L39">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code 1 (line 28):</b>self.full_csv_path = '' \n \
self.dir = '' \n \
self.filename = '' \n \
<b>refactored code1:</b>self.full_csv_path, self.dir, self.filename = '' \n \
 \n \
<b>code 2 (line 33):</b>self.csv = None \n \
self.bokeh_source = None \n \
self.bokeh_source_orig = None \n \
self.last_modified = None \n \
<b>refactored code2:</b>self.csv, self.bokeh_source, self.bokeh_source_orig, self.last_modified = None \n \
 \n \
<b>code 3 (line 38):</b>self.separate_files = False \n \
self.last_reload_data_fix = False \n \
<b>refactored code3:</b>self.separate_files, self.last_reload_data_fix = False"
        },
{
title: 'Refactor the following Python code with a For statement with multiple targets, e.g., <code>"for i in a: print(i[0]) can be refactored into for i_0, *i_remain in a: print(i_0)"</code>. <p><a href="https://github.com/chrismaddalena/ODIN/blob/03fb0044fe658df4d67ffbb8223060958537f17e/lib/htmlreporter.py#L551-L612">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code 1 (line 578):</b>for row in frontable: \n \
    content += '<tr><td>{}</td><td>{}</td><td>{}</td></tr>'.format(row[0], row[1], row[2]) \n \
<b>refactored code1:</b>for (row_0, row_1, row_2, *row_len) in subdomains: \n \
    content += '<tr><td>{}</td><td>{}</td><td>{}</td></tr>'.format(row_0, row_1, row_2) \n \
 \n \
<b>code 2 (line 592):</b>for row in takeovers: \n \
    content += '<tr><td>{}</td><td>{}</td><td>{}</td></tr>'.format(row[0], row[1], row[2]) \n \
<b>refactored code2:</b>for (row_0, row_1, row_2, *row_len) in takeovers: \n \
    content += '<tr><td>{}</td><td>{}</td><td>{}</td></tr>'.format(row_0, row_1, row_2) \n \
 \n \
<b>code 3 (line 605):</b>for row in subdomains: \n \
    content += '<tr><td>{}</td><td>{}</td><td>{}</td></tr>'.format(row[0], row[1], row[2]) \n \
<b>refactored code3:</b>for (row_0, row_1, row_2, *row_len) in subdomains: \n \
    content += '<tr><td>{}</td><td>{}</td><td>{}</td></tr>'.format(row_0, row_1, row_2)"
        },
{
title: 'Refactor the following Python code with While/For statement with else clause, e.g., <code>"for i in a:\n \ pass \n \ else: \n \ pass"</code>. <p><a href="https://github.com/DLLXW/data-science-competition/blob/03490a7ea8e6297211fe8709b61ddad251ae51dd/else/%E5%A4%A9%E9%A9%AC%E6%9D%AF--AI%2Bz%E6%99%BA%E8%83%BD%E8%B4%A8%E6%A3%80/code/uer/utils/tokenizer.py#L350-L397">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code 1 (line 376):</b>is_bad = False \n \
while start < len(chars): \n \
    end = len(chars) \n \
    cur_substr = None \n \
    while start < end: \n \
        substr = ''.join(chars[start:end]) \n \
        if start > 0: \n \
            substr = '##' + six.ensure_str(substr) \n \
        if substr in self.vocab: \n \
            cur_substr = substr \n \
            break \n \
        end -= 1 \n \
    if cur_substr is None: \n \
        is_bad = True \n \
        break \n \
    sub_tokens.append(cur_substr) \n \
    start = end \n \
if is_bad: \n \
    output_tokens.append(self.unk_token) \n \
else: \n \
    output_tokens.extend(sub_tokens) \n \
<b>refactored code1:</b>cur_substr = None \n \
while start < len(chars): \n \
    end = len(chars) \n \
    cur_substr = None \n \
    while start < end: \n \
        substr = ''.join(chars[start:end]) \n \
        if start > 0: \n \
            substr = '##' + six.ensure_str(substr) \n \
        if substr in self.vocab: \n \
            cur_substr = substr \n \
            break \n \
        end -= 1 \n \
    if cur_substr is None: \n \
        output_tokens.append(self.unk_token) \n \
        break \n \
    sub_tokens.append(cur_substr) \n \
    start = end \n \
else: \n \
    output_tokens.extend(sub_tokens) \n \
 \n \
<b>code 2 (line 379): </b>cur_substr = None \n \
while start < end: \n \
    substr = ''.join(chars[start:end]) \n \
    if start > 0: \n \
        substr = '##' + six.ensure_str(substr) \n \
    if substr in self.vocab: \n \
        cur_substr = substr \n \
        break \n \
    end -= 1 \n \
if cur_substr is None: \n \
    is_bad = True \n \
    break \n \
<b>refactored code2:</b>while start < end: \n \
    substr = ''.join(chars[start:end]) \n \
    if start > 0: \n \
        substr = '##' + six.ensure_str(substr) \n \
    if substr in self.vocab: \n \
        cur_substr = substr \n \
        break \n \
    end -= 1 \n \
else: \n \
    is_bad = True \n \
    break"
        },
{
title: 'Refactor the following Python code with Starred in Function Calls, e.g., <code>"func(a[0],a[1]) can be refactored into func(*a[:2])"</code>. <p><a href="https://github.com/neuralchen/SimSwap/blob/a5f6dea67398eec9ee71e156f7ad15dbd7ce4977/test_video_swap_multispecific.py#L33-L98">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code 1 (line 65):</b>specific_person.shape[0], specific_person.shape[1], specific_person.shape[2] \n \
<b>refactored code1:</b>*specific_person.shape[:3] \n \
 \n \
<b>code 2 (line 84):</b>img_a.shape[0], img_a.shape[1], img_a.shape[2] \n \
<b>refactored code2:</b>*img_a.shape[:3]"
        },
{
title: 'Refactor the following Python code with list comprehension, e.g., a= [1 for i in range(2)]. <p><a href="https://github.com/yenchenlin/DeepLearningFlappyBird/blob/5a08d405b68facb784c7dc06ae0f6ad341c2a5a3/deep_q_network.py#L78-L204">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code (line 163):</b>for i in range(0, len(minibatch)): \n \
    terminal = minibatch[i][4] \n \
    if terminal: \n \
        y_batch.append(r_batch[i]) \n \
    else: \n \
        y_batch.append(r_batch[i] + GAMMA * np.max(readout_j1_batch[i])) \n \
<b>refactored code:</b>y_batch = [r_batch[i] if minibatch[i][4] else r_batch[i] + GAMMA * np.max(readout_j1_batch[i]) for i in range(0, len(minibatch))]"
        },
{
title: 'Refactor the following Python code with an Assignment statement using a value to assign multiple targets, e.g., <code>"a=1; b=1 can be refactored into a=b=1"</code>. <p><a href="https://github.com/ansible/ansible-modules-extras/blob/f216ba8e0616bc8ad8794c22d4b48e1ab18886cf/cloud/rackspace/rax_mon_check.py#L144-L262">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code 1 (line 183):</b>should_delete = False \n \
should_create = False \n \
should_update = False \n \
<b>refactored code1:</b>should_delete, should_create, should_update = False"
        },

{
title: 'Refactor the following Python code with truth test, e.g., <code>a != [] can be refactored into a</code>. <p><a href="https://github.com/django/django/blob/c709a748ce6e0759e415a0e3544e0f4ad3d30f8b/django/http/multipartparser.py#L132-L364">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code 1 (line 146):</b>self._content_length == 0 \n \
<b>refactored code1:</b>not self._content_length \n \
 \n \
<b>code 2 (line 309):</b>remaining != 0 \n \
<b>refactored code2:remaining"
        },

{
title: 'Refactor the following Python code with Assignment with multiple targets, e.g., <code>"a = 1; b = c can be refactored into a, b = 1, c pass"</code>. <p><a href="https://github.com/diffgram/diffgram/blob/5bf45ff076ecc9b3192eedae94d2f515cca30edf/default/tests/methods/event/test_user_visit_history.py#L18-L36">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code 1 (line 33):</b>self.project = project_data['project'] \n \
self.project_data = project_data \n \
<b>refactored code1:</b>self.project , self.project_data  = project_data['project'], project_data"
        },

{
title: 'Refactor the following Python code with Starred in Function Calls, e.g., <code>"func(a[0],a[1]) can be refactored into func(*a[:2])"</code>. <p><a href="https://github.com/open-mmlab/OpenPCDet/blob/255db8f02a8bd07211d2c91f54602d63c4c93356/pcdet/ops/pointnet2/pointnet2_batch/pointnet2_modules.py#L61-L99">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code 1 (line 93):</b>mlp_spec[k], mlp_spec[k + 1] \n \
<b>refactored code1:</b>*mlp_spec[k:k+2]"
        },

 {
title: 'Refactor the following Python code with set comprehension, e.g., a= {i for i in range(2)}. <p><a href="https://github.com/mozilla/pontoon/blob/3a95b7bb3c3982cad3320898ee9b791dfa1267ed/pontoon/insights/tasks.py#L386-L429">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code (line 400):</b>for user in privileged_users: \n \
    manager = user['managers_group__user'] \n \
    last_login = user['managers_group__user__last_login'] \n \
    if last_login: \n \
        if last_login + relativedelta(months=months) > start_of_today: \n \
            active_managers.add(manager) \n \
<b>refactored code:</b>active_managers = {user['managers_group__user'] for user in privileged_users if user['managers_group__user__last_login'] if user['managers_group__user__last_login'] + relativedelta(months=months) > start_of_today}"
        },

{
title: 'Refactor the following Python code with fstring to format a string, e.g., <code>a=world; "hello: %s" % a be refactored into f"hello:{a}"</code>. <p><a href="https://github.com/speechbrain/speechbrain/blob/d71a7e018738329fe9a7951d2e9457d52f94edad/recipes/KsponSpeech/ksponspeech_prepare.py#L136-L204">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code 1 (line 163):</b>'Creating csv lists in  %s...' % csv_file \n \
<b>refactored code1:</b>f'Creating csv lists in  {csv_file}...' \n \
 \n \
<b>code 2 (line 203):</b>'%s successfully created!' % csv_file \n \
<b>refactored code2:</b>f'{csv_file} successfully created!'"
        },

{
title: 'Refactor the following Python code with With statement, e.g., <code>"f=open("xxx","r"); pass) can be refactored into with open("xxx","r") as f: pass "</code>. <p><a href="https://github.com/sevagas/macro_pack/blob/071fd4aa16dc74815c2a860ddafe4358d6454c89/src/modules/wsf_gen.py#L27-L48">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code 1 (line 30):</b>f = open(self.getMainVBAFile() + '.vbs') \n \
<b>refactored code1:</b>with open(self.getMainVBAFile() + '.vbs') as f: \n \
    vbsContent = f.read() \n \
 \n \
<b>code 2 (line 42):</b>f = open(self.outputFilePath, 'w') \n \
<b>refactored code2:</b>with open(self.outputFilePath, 'w') as f: \n \
    f.writelines(wsfContent)"
        },

{
title: 'Refactor the following Python code with set comprehension, e.g., a= {i for i in range(2)}. <p><a href="https://github.com/forseti-security/forseti-security/blob/9069bfb04e818e51f484d860a249de0d578d4cf3/google/cloud/forseti/services/dao.py#L2044-L2073">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code 1 (line 2062):</b>for permission in permissions: \n \
    for role in permission.roles: \n \
        roles.add(role) \n \
<b>refactored code1:</b>roles = {role for permission in permissions for role in permission.roles} \n \
 \n \
<b>code 2 (line 2067):</b>for role in roles: \n \
    role_permissions = set([p.name for p in role.permissions]) \n \
    if permission_set.issubset(role_permissions): \n \
        result_set.add(role) \n \
<b>refactored code2:</b>result_set = {role for role in roles if permission_set.issubset(set([p.name for p in role.permissions]))}"
        },

{
title: 'Refactor the following Python code with dict comprehension, e.g., a= {i:i for i in range(2)}. <p><a href="https://github.com/huggingface/transformers/blob/1d7773594754457ed4a79cf6d98bcaabea5bff51/tests/test_modeling_common.py#L2613-L2644">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code 1 (line 2624):</b>for (k, v) in dictionary.items(): \n \
    if isinstance(v, torch.Tensor): \n \
        output[k] = v.to(device) \n \
    else: \n \
        output[k] = v \n \
<b>refactored code1:</b>output = {k: v.to(device) if isinstance(v, torch.Tensor) else v for (k, v) in dictionary.items()}"
        },

{
title: 'Refactor the following Python code with truth test, e.g., <code>a != [] can be refactored into a</code>. <p><a href="https://github.com/geek-ai/MAgent/blob/2144dbd4bef49a8bda499dee95956c3fa04d6a43/examples/train_single.py#L115-L210">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code 1 (line 200):</b>(k+1) % args.render_every == 0 \n \
<b>refactored code1:</b>not (k+1) % args.render_every \n \
 \n \
<b>code 2:  (line 207)</b>(k + 1) % args.save_every == 0 \n \
<b>refactored code2:not (k + 1) % args.save_every"
        },

{
title: 'Refactor the following Python code with Assignment with multiple targets, e.g., <code>"a = 1; b = c can be refactored into a, b = 1, c pass"</code>. <p><a href="https://github.com/facebookresearch/ReAgent/blob/57b58a8b3a6b74bb87a197b73a6cd108ddad895e/reagent/gym/envs/pomdp/pocman.py#L365-L379">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code 1 (line 366):</b>smell_range = self.board['_smell_range'] \n \
agent_pos = self.internal_state.agent_pos \n \
<b>refactored code1:</b>smell_range , agent_pos  = self.board['_smell_range'], self.internal_state.agent_pos \n \
 \n \
<b>code 2 (line 371):</b>smell_x = agent_pos.x + x \n \
smell_y = agent_pos.y + y \n \
<b>refactored code2:</b>smell_x , smell_y  = agent_pos.x + x, agent_pos.y + y"
        },

{
title: 'Refactor the following Python code with dict comprehension, e.g., a= {i:i for i in range(2)}. <p><a href="https://github.com/DATA-DOG/taiga-back/blob/9d54dc63b52b1c5222a904c01ce272169dd26759/taiga/projects/history/models.py#L99-L242">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code 1 (line 151):</b>for (role_id, point_id) in pointsnew.items(): \n \
    role_name = resolve_value('roles', role_id) \n \
    points[role_name] = [None, resolve_value('points', point_id)] \n \
<b>refactored code1:</b>points = {resolve_value('roles', role_id): [None, resolve_value('points', point_id)] for (role_id, point_id) in pointsnew.items()} \n \
 \n \
<b>code 2 (line 156):</b>for (role_id, point_id) in pointsnew.items(): \n \
    role_name = resolve_value('roles', role_id) \n \
    oldpoint_id = pointsold.get(role_id, None) \n \
    points[role_name] = [resolve_value('points', oldpoint_id), resolve_value('points', point_id)] \n \
<b>refactored code2:</b>points.update({resolve_value('roles', role_id): [resolve_value('points', pointsold.get(role_id, None)), resolve_value('points', point_id)] for (role_id, point_id) in pointsnew.items()})"
        },
{
title: 'Refactor the following Python code with With statement, e.g., <code>"f=open("xxx","r"); pass) can be refactored into with open("xxx","r") as f: pass "</code>. <p><a href="https://github.com/golemhq/golem/blob/84f51478b169cdeab73fc7e2a22a64d0a2a29263/golem/report/execution_report.py#L186-L227">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code 1 (line 225):</b>b64 = base64.b64encode(open(image_filename, 'rb').read()).decode('utf-8') \n \
step['screenshot'] = b64 \n \
<b>refactored code1:</b>with open(image_filename, 'rb') as f: \n \
    b64 = base64.b64encode(f.read()).decode('utf-8') \n \
    step['screenshot'] = b64"
        },
{
title: 'Refactor the following Python code with fstring to format a string, e.g., <code>a=world; "hello: %s" % a be refactored into f"hello:{a}"</code>. <p><a href="https://github.com/marin-m/pbtk/blob/777254e0fe38819fddee58ab6cb554c219f74908/utils/descpb_to_proto.py#L134-L183">Right Click the Link to See Code in A New Tab!</a></p>',
references: "<b>code 1 (line 140):</b>'default = %s' % fmt_value(field.default_value) \n \
<b>refactored code1:</b>f'default = {fmt_value(field.default_value)}' \n \
 \n \
<b>code 2 (line 142):</b>'default = \"%s\"' % field.default_value \n \
<b>refactored code2:</b>f'default = \"{field.default_value}\"' \n \
 \n \
<b>code 3 (line 153):</b>'default = %s' % field.default_value \n \
<b>refactored code3:</b>f'default = {field.default_value}' \n \
 \n \
<b>code 4 (line 165):</b>' %s = %s;\n' % (field.name, fmt_value(field.number, field.options, optarr=default)) \n \
<b>refactored code4:</b>f'{field.name} = {fmt_value(field.number, field.options, optarr=default)};\n' \n \
 \n \
<b>code 5 (line 168):</b>'%s %s %s = %s;\n' % (labels[field.label], type_, field.name, fmt_value(field.number, field.options, optarr=default)) \n \
<b>refactored code5:</b>f'{labels[field.label]} {type_} {field.name} = {fmt_value(field.number, field.options, optarr=default)};\n' \n \
 \n \
<b>code 6 (line 170):</b>'%s group %s = %d ' % (labels[field.label], type_, field.number) \n \
<b>refactored code6:</b>f'{labels[field.label]} group {type_} = {field.number} ' \n \
 \n \
<b>code 7 (line 179):</b>'_oneof_%d' % field.oneof_index \n \
<b>refactored code7:</b>f'_oneof_{field.oneof_index}' \n \
 \n \
<b>code 8 (line 180):</b>'_oneof_%d' % field.oneof_index \n \
<b>refactored code8:</b>f'_oneof_{field.oneof_index}'"
        }

      ],
    }
  },
  created() {
    this.createAnswer(this.tupleIndex);
  },
  computed: {
    question() {
      return this.questions[this.questionIndex]
    },
  },
  methods: {
    startTimer() {
      setInterval(() => {
        if (this.seconds > 0) {
          this.seconds--
        } else if (this.minutes > 0) {
          this.minutes--
          this.seconds = 59
        } else {
          // Time is up
          this.ableEdit = false
        }
      }, 1000)
    },
    addQuestion() {
      // Code to add a new question
      this.tupleIndex += 1;
      this.createAnswer(this.tupleIndex);
    },
    createAnswer(index) {
      var answer = {'tuple_id':index};
      this.answers.push(answer);
    },
    resetParameters() {
      this.minutes = 10;
      this.seconds = 0;
      this.tupleIndex = 1;
      this.ableEdit = true;
      this.answers = [];
      this.$refs['reportTuples_1'][0].resetParameters();
      this.createAnswer(this.tupleIndex);
    },
    nextQuestion(){
      console.log(this.$refs)
      for (var i=0;i<this.tupleIndex;i++) {
        this.answers[i]['selectedOption'] = this.$refs['reportTuples_'+(i+1)][0].selectedOption
        this.answers[i]['reason'] = this.$refs['reportTuples_'+(i+1)][0].reason
        this.answers[i]['references'] = this.$refs['reportTuples_'+(i+1)][0].references
      }
      console.log(this.answers)
      this.finalStoredData['answers_'+(this.questionIndex+1)] = {'answers':this.answers, 'min':9-this.minutes,
        'sec':60-this.seconds}

      if (this.questionIndex < 25) {
        this.questionIndex += 1;
        this.resetParameters()
      } else {
        this.saveAsJSON();
        this.$router.push('/thanks');
      }
    },
    saveAsJSON() {
      const data = JSON.stringify(this.finalStoredData, null, 2);
      const filename = this.$route.params.name + '.json';
      const blob = new Blob([data], { type: 'text/plain;charset=utf-8' });
      saveAs(blob, filename);
    }

  },
  mounted() {
    this.startTimer();
  }
}
</script>

<style scoped>
.container1 {
  width: 66.67%;
  margin: auto;
}
.container2 {
  width: 90%;
  margin: auto;
  text-align: left;
  font-size: 1.2rem;
}
.question-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.question-title {
  margin-bottom: 1.5rem;
}
.question-references{
font-size: 0.8rem;
width: 100%;
}
.timer {
  font-size: 1.2rem;
}
.container3 {
  display: flex;
  width: 100%;
  margin-top: 1.5rem;
}
.next-button {
  margin-left: auto;
}
.question-note{
font-size: 0.8rem;
width: 100%;
}
.code {
  display: block;
  white-space: pre-wrap;
  font-size: 0.4rem;
}
</style>