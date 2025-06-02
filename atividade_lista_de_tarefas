from flask import Flask, request, jsonify, Response
from functools import wraps
from werkzeug.security import generate_password_hash, check_password_hash
import jwt
import datetime
import secrets

app = Flask(__name__)
app.config['SECRET_KEY'] = 'secre_key_protection_token'

USERNAME = 'admin'
PASSWORD_HASH = generate_password_hash('123_base64')

tarefas = []

def check_auth(username, password):
    return username == USERNAME and check_password_hash(PASSWORD_HASH, password)

def authenticate():
    return Response('Login necessário', 401, {'WWW-Authenticate': 'Basic realm="Login Required"'})

def requires_basic_auth(f):
    @wraps(f)
    def decorated(*args, **kwargs):
        auth = request.authorization
        if not auth or not check_auth(auth.username, auth.password):
            return authenticate()
        return f(*args, **kwargs)
    return decorated

def token_required(f):
    @wraps(f)
    def decorated(*args, **kwargs):
        token = None
        auth_header = request.headers.get('Authorization', '')
        if auth_header.startswith('Bearer '):
            token = auth_header.split(' ')[1]
        if not token:
            return jsonify({'message': 'Token ausente!'}), 401
        try:
            jwt.decode(token, app.config['SECRET_KEY'], algorithms=['HS256'])
        except jwt.ExpiredSignatureError:
            return jsonify({'message': 'Token expirado!'}), 401
        except jwt.InvalidTokenError:
            return jsonify({'message': 'Token inválido!'}), 401
        return f(*args, **kwargs)
    return decorated

@app.route('/login', methods=['POST'])
@requires_basic_auth
def login():
    token = jwt.encode({
        'user': USERNAME,
        'exp': datetime.datetime.utcnow() + datetime.timedelta(hours=1)
    }, app.config['SECRET_KEY'], algorithm='HS256')
    return jsonify({'token': token})

@app.route('/tarefas', methods=['GET'])
def listar_tarefas():
    if not tarefas:
        return jsonify(message="Nenhuma tarefa encontrada.", tarefas=[], total=0)
    return jsonify(tarefas=tarefas, total=len(tarefas))

@app.route('/tarefas', methods=['POST'])
def adicionar_tarefa():
    data = request.get_json()
    descricao = data.get('descricao')
    tarefa = {"id": secrets.token_hex(2), "descricao": descricao, "status": "pendente"}
    tarefas.append(tarefa)
    return jsonify(message="Tarefa adicionada com sucesso!", tarefa=tarefa), 201

@app.route('/tarefas/<string:id>', methods=['PUT'])
@token_required
def atualizar_tarefa(id):
    data = request.get_json()
    for tarefa in tarefas:
        if tarefa['id'] == id:
            tarefa['descricao'] = data.get('descricao', tarefa['descricao'])
            tarefa['status'] = data.get('status', tarefa['status'])
            return jsonify(message="Tarefa atualizada!", tarefa=tarefa)
    return jsonify(message="Tarefa não encontrada."), 404

@app.route('/tarefas/<string:id>/pendente', methods=['PATCH'])
@token_required
def marcar_como_pendente(id):
    for tarefa in tarefas:
        if tarefa['id'] == id:
            tarefa['status'] = 'pendente'
            return jsonify(message="Tarefa marcada como pendente!", tarefa=tarefa)
    return jsonify(message="Tarefa não encontrada."), 404

@app.route('/tarefas/<string:id>/concluida', methods=['PATCH'])
@token_required
def marcar_como_concluida(id):
    for tarefa in tarefas:
        if tarefa['id'] == id:
            tarefa['status'] = 'concluída'
            return jsonify(message="Tarefa marcada como concluída!", tarefa=tarefa)
    return jsonify(message="Tarefa não encontrada."), 404

@app.route('/tarefas/<string:id>', methods=['DELETE'])
@token_required
def remover_tarefa(id):
    global tarefas
    for tarefa in tarefas:
        if tarefa['id'] == id:
            tarefas = [t for t in tarefas if t['id'] != id]
            return jsonify(message=f"Tarefa '{tarefa['descricao']}' removida com sucesso!")
    return jsonify(message="Tarefa não encontrada."), 404
